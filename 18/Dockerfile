FROM buildpack-deps:jessie

ENV OTP_VERSION=18.1.3 LANG=C.UTF-8

# We'll install the build dependencies for erlang-odbc along with the erlang
# build process:
RUN set -xe \
	&& OTP_DOWNLOAD_SHA1=981e6c03c0a310483e6c14edb7dd2acaa842e2f8 \
	&& buildDeps='unixodbc-dev' \
	&& apt-get update \
	&& apt-get install -y --no-install-recommends $buildDeps \
	&& mkdir -p /usr/src/otp-src \
	&& curl -fSL -o otp-src.tar.gz "https://github.com/erlang/otp/archive/OTP-$OTP_VERSION.tar.gz" \
	&& echo "$OTP_DOWNLOAD_SHA1 otp-src.tar.gz" | sha1sum -c - \
	&& tar -xzf otp-src.tar.gz -C /usr/src/otp-src --strip-components=1 \
	&& rm otp-src.tar.gz \
	&& cd /usr/src/otp-src \
	&& ./otp_build autoconf \
	&& ./configure \
	&& make -j$(nproc) \
	&& make install \
	&& find /usr/local -name examples |xargs rm -rf \
	&& rm -rf /usr/src/otp-src \
	&& apt-get purge -y --auto-remove $buildDeps

CMD ["erl"]

# extra useful tools here
ENV REBAR_VERSION=2.6.1

RUN set -xe \
	&& REBAR_DOWNLOAD_SHA1=7f9b799c5dfa06d1779d828c767be2732dacc411 \
	&& mkdir -p /usr/src/rebar-src \
	&& curl -fSL -o rebar-src.tar.gz "https://github.com/rebar/rebar/archive/$REBAR_VERSION.tar.gz" \
	&& echo "$REBAR_DOWNLOAD_SHA1 rebar-src.tar.gz" | sha1sum -c - \
	&& tar -xzf rebar-src.tar.gz -C /usr/src/rebar-src --strip-components=1 \
	&& rm rebar-src.tar.gz \
	&& cd /usr/src/rebar-src \
	&& ./bootstrap \
	&& install -v ./rebar /usr/local/bin/ \
	&& rm -rf /usr/src/rebar-src
