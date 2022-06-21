# https://developers.home-assistant.io/docs/add-ons/configuration#add-on-dockerfile
ARG BUILD_FROM
FROM $BUILD_FROM

# Execute during the build of the image
ARG TEMPIO_VERSION BUILD_ARCH
RUN \
    curl -sSLf -o /usr/bin/tempio \
    "https://github.com/home-assistant/tempio/releases/download/${TEMPIO_VERSION}/tempio_${BUILD_ARCH}"

# Duplicati ARG & ENV
ARG DUPLICATI_VER=2.0.6.104-2.0.6.104_canary_2022-06-15
ENV DUPLICATI_VER ${DUPLICATI_VER}

ARG DUPLICATI_DOWNLOAD_URL=https://github.com/duplicati/duplicati/releases/download/v${DUPLICATI_VER}/duplicati-${DUPLICATI_VER}.zip
ENV DUPLICATI_DOWNLOAD_URL ${DUPLICATI_DOWNLOAD_URL}

RUN apk add --no-cache mono --repository http://dl-cdn.alpinelinux.org/alpine/edge/testing

RUN apk add --no-cache --virtual=.build-dependencies ca-certificates

RUN cert-sync /etc/ssl/certs/ca-certificates.crt

RUN apk del .build-dependencies

RUN set -xe && \
	echo "**** install duplicati ****" && \
	mkdir -p /app/duplicati && \
	if [ -z ${VERSION} ]; then \
		VERSION=$(curl -sL "https://api.github.com/repos/duplicati/duplicati/releases" | \
			jq -r 'first(.[] | select(.tag_name | contains("beta"))) | .tag_name'); \
	fi && \
	curl -o \
		/tmp/duplicati.zip -L \
		"$(curl -s https://api.github.com/repos/duplicati/duplicati/releases/tags/${VERSION} | \
			jq -r '.assets[].browser_download_url' | grep '.zip$' | grep -v signatures)" && \
	unzip \
		/tmp/duplicati.zip -d \
		/app/duplicati && \
	echo "**** cleanup ****" && \
	rm -rf \
		/tmp/*

# Copy root filesystem
COPY rootfs /


# ports and volumes
EXPOSE 8200
VOLUME /config /source /backups