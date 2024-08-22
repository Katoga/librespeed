FROM golang:1.23-alpine3.20 AS builder

WORKDIR /opt/librespeed
ARG librespeed_version

# hadolint ignore=DL3018
RUN apk add --no-cache \
    bash \
    git \
    upx \
  && git clone https://github.com/librespeed/speedtest-cli . \
  && git reset --hard "v${librespeed_version}" \
  && go get -u \
  && go mod tidy \
  && ./build.sh


FROM python:3-alpine3.20 AS runner

RUN apk upgrade --no-cache \
  && pip install --no-cache-dir \
    prometheus-client~=0.20.0

COPY --from=builder /opt/librespeed/out/librespeed-cli-* /usr/local/bin/librespeed-cli

COPY ./exporter/ /opt/librespeed/exporter/

ENTRYPOINT [ "python", "/opt/librespeed/exporter/server.py" ]

USER nobody
