FROM node:10-alpine
LABEL maintainer "h-mineta <h-mineta@0nyx.net>"

ENV DOCKER="YES"
ARG REPOSITORY="git://github.com/Chinachu/Chinachu.git"
ARG BRANCH="master"

ARG WORK_DIR="/usr/local/chinachu"

#node container default value
ARG USER_NAME="node"

RUN set -x \
	&& apk upgrade --update \
	&& apk add \
		bash \
		coreutils \
		curl \
		procps \
		ca-certificates \
		ffmpeg-libs \
		raspberrypi-libs \
	\
	&& apk add --virtual .build-deps \
		git \
		make \
		gcc \
		g++ \
		autoconf \
		automake \
		cmake \
		wget \
		curl \
		sudo \
		tar \
		xz \
		libc-dev \
		musl-dev \
		eudev-dev \
		libevent-dev \
		build-base \
		coreutils \
		linux-headers \
		gnutls-dev lame-dev libvorbis-dev xvidcore-dev zlib-dev libvdpau-dev imlib2-dev x264-dev libtheora-dev coreutils bzip2-dev perl-dev libvpx-dev sdl2-dev libxfixes-dev libva-dev alsa-lib-dev v4l-utils-dev yasm opus-dev x265-dev libass-dev raspberrypi-dev\
	\
	# install omx enabled ffmpeg
	&& git clone https://git.ffmpeg.org/ffmpeg.git --branch release/4.2 --depth 1 /tmp/ffmpeg\
	&& cd /tmp/ffmpeg \
	&& env CFLAGS="-I/opt/vc/include/IL" ./configure \
		--prefix=/usr \
		--enable-avresample \
		--enable-avfilter \
		--enable-gnutls \
		--enable-gpl \
		--enable-libass \
		--enable-libmp3lame \
		--enable-libvorbis \
		--enable-libvpx \
		--enable-libxvid \
		--enable-libx264 \
		--enable-libx265 \
		--enable-libtheora \
		--enable-libv4l2 \
		--enable-postproc \
		--enable-pic \
		--enable-pthreads \
		--enable-shared \
		--enable-libxcb \
		--disable-stripping \
		--disable-static \
		--disable-debug \
		--disable-librtmp \
		--enable-vaapi \
		--enable-vdpau \
		--enable-libopus \
		--enable-omx \
		--enable-omx-rpi \
	&& make -j4 \
	&& make install \
	# install chinachu
	&& mkdir -p ${WORK_DIR} \
	&& git clone ${REPOSITORY} ${WORK_DIR} \
	&& chown -R ${USER_NAME} ${WORK_DIR} \
	&& cd ${WORK_DIR} \
	&& git checkout ${BRANCH} \
	#install git sumbodules
	&& echo 2 | sudo -u ${USER_NAME} ./chinachu installer \
	#install npm modules
	&& echo 4 | sudo -u ${USER_NAME} ./chinachu installer \
	&& sudo -u ${USER_NAME} ./chinachu service operator initscript | tee /tmp/chinachu-operator \
	&& sudo -u ${USER_NAME} ./chinachu service wui initscript | tee /tmp/chinachu-wui \
	&& sudo -u ${USER_NAME} mkdir log \
	\
	#&& chown root. /tmp/chinachu-operator /tmp/chinachu-wui \
	&& chmod u+x /tmp/chinachu-operator /tmp/chinachu-wui \
	&& mv /tmp/chinachu-operator /etc/init.d/ \
	&& mv /tmp/chinachu-wui /etc/init.d/ \
	\
	# cleaning
	&& cd / \
	&& npm cache verify \
	&& apk del --purge .build-deps \
	&& rm -rf /tmp/ffmpeg \
	&& rm -rf /var/cache/apk/*

	# forward request and error logs to docker log collector
	#&& ln -sf /dev/stdout ${WORK_DIR}/log/operator \
	#&& ln -sf /dev/stdout ${WORK_DIR}/log/scheduler \
	#&& ln -sf /dev/stdout ${WORK_DIR}/log/wui

COPY services.sh /usr/local/bin
COPY config.sample.json ${WORK_DIR}

WORKDIR ${WORK_DIR}
CMD ["/usr/local/bin/services.sh"]
EXPOSE 10772 20772 5353
