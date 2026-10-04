# Alloy config-reloader runtime

This DRJ packaging variant builds the v0.94.1 reloader from source with the
existing `make prometheus-config-reloader-image` target. Its immutable
distroless static final stage preserves numeric nobody ownership (65534:65534),
the `/` working directory, CA roots and the native binary entrypoint. It has no
shell, curl or wget.

Use it only for the Morningwood Alloy sidecar's native read-only
`--watched-dir=/etc/alloy` and HTTP reload of `localhost:12345/-/reload`, without
exec probes or an envsubst output file. Existing collector inputs remain owned
by their chart; this source change does not select or deploy an image.

This is **not** a general replacement for the stock Prometheus Operator
reloader image. `ListenLocal(true)` with `--enable-config-reloader-probes`
generates startup, readiness and liveness exec probes using `sh -c` and
curl/wget. Those probes cannot run in this image. The Operator's default stock
image, probe generation and other image Dockerfiles remain unchanged.

Actual ARM64 static linkage, numeric user/CA contents, read-only watch and HTTP
reload behavior, and zero HIGH/CRITICAL image findings must be qualified before
the chart selects this variant. Native Go source unit tests do not establish
those image/runtime properties.
