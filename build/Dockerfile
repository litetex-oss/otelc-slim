# Docs https://opentelemetry.io/docs/collector/extend/ocb/#containerize-your-collector-distribution

FROM otel/opentelemetry-collector-builder:0.149.0 AS builder

COPY ./builder-config.yml /build/builder-config.yaml

RUN /usr/local/bin/ocb --config=/build/builder-config.yaml


# Based on alpine instead of distroless
# Why? So that you can exec into it and troubleshoot...
FROM alpine:3

ARG USER_UID=1000
USER ${USER_UID}

COPY ./collector-config.yml /otelcol/collector-config.yml
COPY --chmod=755 --from=builder /home/ocb/output/otelcol-slim /otelcol

ENTRYPOINT ["/otelcol/otelcol-slim"]
CMD ["--config", "/otelcol/collector-config.yml"]
