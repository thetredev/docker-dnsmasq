## container image configuration

# dnsmasq release settings
ARG DNSMASQ_VERSION=2.92

# webproc release settings
ARG WEBPROC_VERSION=v0.4.0
ARG WEBPROC_URL=https://github.com/jpillora/webproc.git


## container image build instructions
FROM alpine:3.23.2 AS webproc

# build webproc binary
ARG WEBPROC_VERSION
ARG WEBPROC_URL

ENV CGO_ENABLED=0 \
    GOOS=linux \
    GOARCH=amd64

WORKDIR /srv/build
RUN <<EOF
  apk add --no-cache \
    git \
    go

  git clone ${WEBPROC_URL}
  cd webproc
  git checkout ${WEBPROC_VERSION}

  go get -u ./...
  go mod tidy

  go build -ldflags "-s -w"
  mv webproc /srv/webproc
EOF


FROM alpine:3.23.2 AS dnsmasq
ARG DNSMASQ_VERSION

# build dnsmasq binary
WORKDIR /srv/build
RUN <<EOF
  apk add --no-cache \
    dnsmasq \
    xz \
    musl-dev \
    gcc \
    make \
    linux-headers

  wget -O - https://thekelleys.org.uk/dnsmasq/dnsmasq-${DNSMASQ_VERSION}.tar.xz | xz -d - | tar xf - --strip-components=1
  make -j $(nproc) LDFLAGS='-static'
  strip src/dnsmasq
EOF

WORKDIR /srv/dnsmasq
RUN <<EOF
  mkdir -p bin etc

  mv /srv/build/src/dnsmasq /srv/dnsmasq/bin/dnsmasq
  cp /etc/group etc
  cp /etc/passwd etc
EOF


FROM scratch

# see https://docs.docker.com/reference/dockerfile/#maintainer-deprecated
LABEL com.github.jpillora.docker-dnsmasq.authors="dev@jpillora.com thetredev@gmail.com"

COPY --from=webproc --chmod=0744 /srv/webproc /apps/webproc/bin/webproc
COPY --from=dnsmasq --chmod=0744 /srv/dnsmasq/bin /apps/dnsmasq/bin
COPY --from=dnsmasq /srv/dnsmasq/etc /etc

# configure dnsmasq
COPY --link=true rootfs/etc /etc

# configure environment variables
ENV PATH=/apps/webproc/bin:/apps/dnsmasq/bin: \
    LC_ALL=C \
    LANG=C \
    LANGUAGE=C \
    TERM=xterm-256color

# run!
ENTRYPOINT ["webproc", "-c", "/etc/dnsmasq.conf", "--", "dnsmasq", "--no-daemon"]
