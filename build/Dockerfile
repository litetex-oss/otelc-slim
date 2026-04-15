# Docs https://opentelemetry.io/docs/collector/extend/ocb/#containerize-your-collector-distribution

FROM otel/opentelemetry-collector-builder:0.150.0 AS builder

# sum.golang.org is must be run by idiots because otherwise I can't explain 
# how this server reguarily (2 occurences in less than 1 week!) fails to connect to GitHub
# or why this is the default
# -> Disable this for my orga and contact GitHub directly
#
# Docs:
# * https://go.dev/ref/mod#environment-variables
# * https://go.dev/ref/mod#private-module-privacy
ENV GONOSUMDB=github.com/litetex-oss/*

USER root

# Add git so when the go dep resolution fails it prints out a human understandable error 
# and not "git is missing"
RUN apk add --no-cache git

USER ocb

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
