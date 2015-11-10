# The goal is to provide an official docker image with Phoenix to work with the
# official Phoenix "Getting Started" guide at
# http://www.phoenixframework.org/docs/installation, which we'll assume is the
# common case for the majority of Phoenix projects. That wuold require us to
# have:
# - The Phoenix package installed on the "system" Erlang/Elixir.
# - A NodeJS version for the burnch.io stuff...
# - The Postgres Client library package for ecto-postgres to work.
# - The inotify-tools package for the code-reload stuff to work.

# This would provide a 'base' official image. However, we'd discuss whether or
# not maintain a 'slim' docker image for Phoenix, as it's really easy to just
# use the 'slim' elixir image and install nodejs "temporarily" for compiling
# assets during build time.

# 1: Begin with the elixir image, which should include Erlang+rebar & Elixir+Hex:
FROM elixir:1.1.1

# 2: Make a base install of Phoenix... which is just needed for... wtf???
ENV PHOENIX_VERSION=1.0.3
RUN PHOENIX_DOWNLOAD_SHA512=68cd993932140cde233a0ac35a9927799e58b81235b9e8a95d75800137eaebf9f6a603131faa7b9c5b4cac049f5f8590fd73275d83fb6c59462ab61bd7b0605a \
 && mix archive.install --force --sha512 $PHOENIX_DOWNLOAD_SHA512 \
    https://github.com/phoenixframework/phoenix/releases/download/v${PHOENIX_VERSION}/phoenix_new-${PHOENIX_VERSION}.ez

# 3: Install NodeJS from dist, as debian:jessie's nodejs is outdated for use with Phoenix:
RUN NODE_VERSION=5.0.0 \
 && set -ex \
 && for key in \
   9554F04D7259F04124DE6B476D5A82AC7E37093B \
   94AE36675C464D64BAFA68DD7434390BDBE9B9C5 \
   0034A06D9D9B0064CE8ADF6BF1747F4AD2306D93 \
   FD3A5288F042B6850C66B31F09FE44734EB7990E \
   71DCFD284A79C3B38668286BC97EC7A07EDE3FC1 \
   DD8F2338BAE7501E3DD5AC78C273792F7D83545D \
 ; do \
   gpg --keyserver ha.pool.sks-keyservers.net --recv-keys "$key"; \
 done \
 && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.gz" \
 && curl -SLO "https://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
 && gpg --verify SHASUMS256.txt.asc \
 && grep " node-v$NODE_VERSION-linux-x64.tar.gz\$" SHASUMS256.txt.asc | sha256sum -c - \
 && tar -xzf "node-v$NODE_VERSION-linux-x64.tar.gz" -C /usr/local --strip-components=1 \
 && rm "node-v$NODE_VERSION-linux-x64.tar.gz" SHASUMS256.txt.asc

# 4: Install postgres client used by ecto-postgres, and the inotify-tools:
RUN apt-get update \
 && apt-get install -y postgresql-client inotify-tools \
 && rm -rf /var/lib/apt/lists/*

EXPOSE 4000
CMD ["mix","phoenix.server"]
