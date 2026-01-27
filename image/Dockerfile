FROM alpine:3.23.2

# see https://docs.docker.com/reference/dockerfile/#maintainer-deprecated
LABEL com.github.jpillora.docker-dnsmasq.authors="dev@jpillora.com thetredev@gmail.com"

# webproc release settings
ARG WEBPROC_VERSION=0.2.2
ARG WEBPROC_URL=https://github.com/jpillora/webproc/releases/download/$WEBPROC_VERSION/webproc_linux_amd64.gz

# fetch dnsmasq and webproc binary
RUN apk update \
	&& apk --no-cache add dnsmasq \
	&& apk add --no-cache --virtual .build-deps curl \
	&& curl -sL $WEBPROC_URL | gzip -d - > /usr/local/bin/webproc \
	&& chmod +x /usr/local/bin/webproc \
	&& apk del .build-deps

# configure dnsmasq
COPY --link=true rootfs/etc /etc

# run!
ENTRYPOINT ["webproc", "--config", "/etc/dnsmasq.conf", "--", "dnsmasq", "--no-daemon"]
