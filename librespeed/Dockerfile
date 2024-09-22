FROM golang:1.23-alpine3.20 AS builder

ARG speedtest_cli_version

# hadolint ignore=DL3018
RUN apk add --no-cache \
    git \
    upx \
  && go install "github.com/librespeed/speedtest-cli@v${speedtest_cli_version}" \
  && upx -9 /go/bin/speedtest-cli


FROM python:3-alpine3.20 AS runner

RUN apk upgrade --no-cache \
  && pip install --no-cache-dir \
    prometheus-client~=0.20.0

COPY --from=builder /go/bin/speedtest-cli /usr/local/bin/speedtest-cli

COPY ./exporter/ /opt/librespeed/exporter/

ENTRYPOINT [ "python", "/opt/librespeed/exporter/server.py" ]

USER nobody
