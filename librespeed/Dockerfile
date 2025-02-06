FROM golang:1.23-alpine3.21 AS builder-base

ENV CGO_ENABLED=1

# hadolint ignore=DL3018
RUN apk upgrade --no-cache \
  && apk add --no-cache \
    build-base \
    git \
    upx


FROM builder-base AS builder-speedtest-cli

ARG speedtest_cli_version
RUN go install \
    -tags 'netgo' \
    -ldflags "-w -s -linkmode 'external' -extldflags '-static'" \
    "github.com/librespeed/speedtest-cli@v${speedtest_cli_version}" \
  && upx -9 /go/bin/speedtest-cli


FROM builder-base AS builder-librespeed_exporter

ARG librespeed_exporter_version
RUN go install \
    -tags 'netgo' \
    -ldflags "-w -s -linkmode 'external' -extldflags '-static'" \
    "github.com/Katoga/librespeed_exporter@v${librespeed_exporter_version}" \
  && upx -9 /go/bin/librespeed_exporter


FROM gcr.io/distroless/static-debian12:nonroot AS runner

COPY --from=builder-speedtest-cli /go/bin/speedtest-cli /usr/bin/speedtest-cli
COPY --from=builder-librespeed_exporter /go/bin/librespeed_exporter /usr/bin/librespeed_exporter

CMD [ "/usr/bin/librespeed_exporter", "--librespeed.command", "/usr/bin/speedtest-cli", "--librespeed.server", "79" ]
