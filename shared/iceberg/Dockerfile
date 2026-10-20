# syntax=docker/dockerfile:1.16.0@sha256:e2dd261f92e4b763d789984f6eab84be66ab4f5f08052316d8eb8f173593acf7
# check=error=true

FROM local-image/java-devel AS builder

ARG PRODUCT_VERSION
ARG RELEASE_VERSION
ARG STACKABLE_USER_UID
# Optional: Java (and thus the Gradle wrapper) ignores HTTP(S)_PROXY env vars
ARG HTTP_PROXY=""
ARG HTTPS_PROXY=""
ARG NO_PROXY=""

COPY --chown=${STACKABLE_USER_UID}:0 shared/iceberg/stackable/patches/patchable.toml /stackable/src/shared/iceberg/stackable/patches/patchable.toml
COPY --chown=${STACKABLE_USER_UID}:0 shared/iceberg/stackable/patches/${PRODUCT_VERSION} /stackable/src/shared/iceberg/stackable/patches/${PRODUCT_VERSION}

USER ${STACKABLE_USER_UID}
WORKDIR /stackable

RUN <<EOF
cd "$(/stackable/patchable --images-repo-root=src checkout shared/iceberg ${PRODUCT_VERSION})"

# Publishes the artifacts to /stackable/.m2/repository with the unchanged upstream version,
# so that Trino picks them up instead of the upstream ones.
./gradlew --no-daemon publishToMavenLocal \
  -x test \
  -x javadoc \
  -x generateGitProperties
EOF
