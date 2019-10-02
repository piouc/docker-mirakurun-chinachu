FROM node:10-alpine
LABEL maintainer "h-mineta <h-mineta@0nyx.net>"

ENV DOCKER="YES"
ENV LD_LIBRARY_PATH="/usr/local/lib"

RUN set -x \
	&& apk upgrade --update \
	&& apk add bash coreutils libusb pcsc-lite pcsc-lite-libs curl ca-certificates util-linux \
	&& apk add --virtual .build-deps git make cmake gcc g++ gzip autoconf automake libc-dev musl-dev eudev-dev libevent-dev pcsc-lite-dev libusb-dev libtool flex linux-headers \
	&& npm config set unsafe-perm true \
	&& npm install pm2 -g \
	\
	&& npm install arib-b25-stream-test -g --unsafe \
	\
	# mirakurun
	&& npm install mirakurun@latest -g --unsafe --production \
	\
	#libarib25
	&& git clone https://github.com/stz2012/libarib25 /tmp/libarib25 \
	&& mkdir /tmp/libarib25/build \
	&& cd /tmp/libarib25/build \
	&& cmake .. \
	&& make \
	&& make install \
	&& ldconfig \
	#recdvb
	&& git clone https://github.com/piouc/recdvb /tmp/recdvb \
	&& cd /tmp/recdvb/ \
	&& ./autogen.sh \
	&& ./configure --enable-b25 \
	&& make \
	&& make install \
	# ccid
	&& git clone --recursive https://salsa.debian.org/rousseau/CCID.git /tmp/CCID \
	&& cd /tmp/CCID \
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
	&& rm -rf /tmp/libarib25 \
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
