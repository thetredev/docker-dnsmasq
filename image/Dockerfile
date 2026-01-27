FROM alpine:3.23.2

# see https://docs.docker.com/reference/dockerfile/#maintainer-deprecated
LABEL com.github.jpillora.docker-dnsmasq.authors="dev@jpillora.com thetredev@gmail.com"

# webproc release settings
ARG WEBPROC_VERSION=0.4.0
ARG WEBPROC_URL=https://github.com/jpillora/webproc/releases/download/v${WEBPROC_VERSION}/webproc_${WEBPROC_VERSION}_linux_amd64.gz

# fetch dnsmasq and webproc binary
RUN <<EOF
  apk --no-cache add dnsmasq
  apk add --no-cache --virtual .build-deps curl
  curl -sL $WEBPROC_URL | gzip -d - > /usr/local/bin/webproc
  chmod +x /usr/local/bin/webproc
  apk del .build-deps
EOF

# configure dnsmasq
COPY --link=true rootfs/etc /etc

# run!
ENTRYPOINT ["webproc", "-c", "/etc/dnsmasq.conf", "--", "dnsmasq", "--no-daemon"]
