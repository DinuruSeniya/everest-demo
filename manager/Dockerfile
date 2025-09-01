# Stage 1: Build
FROM ghcr.io/catarial/everest-ci/build-kit-base:v1.5.2 AS builder

ARG EVEREST_VERSION=2025.6.0
ENV EVEREST_VERSION=${EVEREST_VERSION}

COPY . /tmp/

RUN bash /tmp/os-pkg-install.sh \
    && bash /tmp/initial-install.sh \
    && /entrypoint.sh run-script compile \
    && /entrypoint.sh run-script install \
    && bash /tmp/demo-patch-scripts/apply-library-patches.sh \
    && /entrypoint.sh run-script compile \
    && /entrypoint.sh run-script install \
    && bash /tmp/demo-patch-scripts/apply-runtime-patches.sh

COPY run-test.sh /ext/source/tests/run-test.sh
COPY run-ocpp-tests.sh /ext/source/tests/run-ocpp-tests.sh

# Stage 2: Runtime
FROM debian:bookworm-slim

# Install only runtime dependencies (adjust as needed)
RUN apt-get update && apt-get install -y \
    libssl3 \
    && rm -rf /var/lib/apt/lists/*

# Copy installed EVerest from builder
COPY --from=builder /ext /ext
COPY --from=builder /usr/local /usr/local

WORKDIR /ext
ENTRYPOINT ["/usr/local/bin/everest-manager"]

LABEL org.opencontainers.image.source="https://github.com/everest/everest-demo"