FROM golang:1.21-alpine3.19 AS builder

WORKDIR /opt/librespeed
ARG librespeed_version
RUN apk add --no-cache \
    bash \
    git \
  && git clone https://github.com/librespeed/speedtest-cli . \
  && git reset --hard "v${librespeed_version}" \
  && ./build.sh


FROM python:3-alpine3.19 as runner

RUN apk upgrade --no-cache \
  && pip install --no-cache-dir \
    prometheus-client~=0.19.0

COPY --from=builder /opt/librespeed/out/librespeed-cli-* /usr/local/bin/librespeed-cli

COPY ./exporter/ /opt/librespeed/exporter/

ENTRYPOINT [ "python", "/opt/librespeed/exporter/server.py" ]

USER nobody
