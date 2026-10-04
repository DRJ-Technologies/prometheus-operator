ARG GOARCH=amd64
ARG OS=linux
ARG GOLANG_BUILDER=1.26

FROM quay.io/prometheus/golang-builder:${GOLANG_BUILDER}-base AS builder
WORKDIR /workspace

COPY . .

# Download Go dependencies to reuse the Go cache in subsequent builds.
RUN --mount=type=cache,target=/go/pkg/mod --mount=type=cache,target=/root/.cache/go-build go mod download -x && go mod verify

# Build
ARG GOARCH
ENV GOARCH=${GOARCH}
RUN --mount=type=cache,target=/go/pkg/mod --mount=type=cache,target=/root/.cache/go-build make prometheus-config-reloader

# Alloy-only runtime: no shell/exec probes. See README.md in this directory.
FROM --platform=${OS}/${GOARCH} gcr.io/distroless/static-debian13:nonroot@sha256:e2e927ec666bae08560abb3c55d0659eceabb657f56b6782ab500a9fc7f555e3
WORKDIR /

COPY --from=builder workspace/prometheus-config-reloader /bin/prometheus-config-reloader

USER 65534:65534

ENTRYPOINT ["/bin/prometheus-config-reloader"]
