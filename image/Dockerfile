## container image configuration
# base image release settings
ARG BASE_IMAGE=alpine:3.23.2

# webproc release settings
ARG WEBPROC_VERSION=0.4.0
ARG WEBPROC_URL=https://github.com/jpillora/webproc/releases/download/v${WEBPROC_VERSION}/webproc_${WEBPROC_VERSION}_linux_amd64.gz


## container image build instructions
FROM ${BASE_IMAGE}

# see https://docs.docker.com/reference/dockerfile/#maintainer-deprecated
LABEL com.github.jpillora.docker-dnsmasq.authors="dev@jpillora.com thetredev@gmail.com"

# fetch dnsmasq and webproc binary
ARG WEBPROC_URL
RUN <<EOF
  apk --no-cache add dnsmasq
  wget -O - ${WEBPROC_URL} | gzip -d - > /usr/local/bin/webproc
  chmod +x /usr/local/bin/webproc
EOF

# configure dnsmasq
COPY --link=true rootfs/etc /etc

# run!
ENTRYPOINT ["webproc", "-c", "/etc/dnsmasq.conf", "--", "dnsmasq", "--no-daemon"]
