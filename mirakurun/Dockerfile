FROM node:10-alpine
LABEL maintainer "h-mineta <h-mineta@0nyx.net>"

ENV DOCKER="YES"

RUN set -x \
	&& apk upgrade --update \
	&& apk add \
		bash \
		coreutils \
		libusb \
		pcsc-lite \
		pcsc-lite-libs \
		curl \
		ca-certificates \
		util-linux \
	\
	&& apk add --virtual .build-deps \
		git \
		make \
		gcc \
		g++ \
		gzip \
		autoconf \
		automake \
		libc-dev \
		musl-dev \
		eudev-dev \
		libevent-dev \
		pcsc-lite-dev \
		libusb-dev \
		libtool\
		flex\
	\
	&& npm config set unsafe-perm true \
	&& npm install pm2 -g \
	\
	&& npm install arib-b25-stream-test -g --unsafe \
	\
	# mirakurun
	&& npm install mirakurun@latest -g --unsafe --production \
	\
	#recdvb
	&& git clone https://github.com/qpe/recdvb /tmp/recdvb \
	&& cd /tmp/recdvb/ \
	&& ./autogen.sh \
	&& ./configure --enable-b25 \
	&& make \
	&& make install \
	# ccid
	&& cd /tmp \
	&& git clone --recursive https://salsa.debian.org/rousseau/CCID.git \
	&& cd CCID \
	&& ./bootstrap \
	&& ./configure \
	&& make \
	&& make install \
	\
	# cleaning
	&& cd / \
	&& npm cache verify \
	&& apk del --purge .build-deps \
	#&& rm -rf /tmp/* \
	&& rm -rf /tmp/recdvb \
	&& rm -rf /tmp/ccid-* \
	&& rm -rf /var/cache/apk/*

	# forward request and error logs to docker log collector
	#&& ln -sf /dev/stdout /usr/local/var/log/mirakurun.stdout-0.log \
	#&& ln -sf /dev/stderr /usr/local/var/log/mirakurun.stderr-0.log

COPY services.sh /usr/local/bin

WORKDIR /usr/local/lib/node_modules/mirakurun
CMD ["/usr/local/bin/services.sh"]
EXPOSE 40772
