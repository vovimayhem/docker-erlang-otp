FROM debian:jessie

ENV OTP_VERSION 18.1.3

# We'll install the build dependencies, and purge them on the last step to make
# sure our final image contains only what we've just built:
RUN set -xe \
	&& OTP_DOWNLOAD_SHA1=981e6c03c0a310483e6c14edb7dd2acaa842e2f8 \
	&& buildDeps=' \
		autoconf \
		bison \
		ca-certificates \
		curl \
		gcc \
		g++ \
		libbz2-dev \
		libgdbm-dev \
		libglib2.0-dev \
		libncurses-dev \
		libodbc1 \
		libreadline-dev \
		libssl-dev \
		libxml2-dev \
		libxslt-dev \
		unixodbc-dev \
		make \
	' \
	&& apt-get update \
	&& apt-get install -y --no-install-recommends $buildDeps \
	&& rm -rf /var/lib/apt/lists/* \
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
