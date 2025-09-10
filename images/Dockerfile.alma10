FROM quay.io/kairos/kairos-init:v0.6.0@sha256:b326c95650bd4c68422330f414a03e0a7f0f89620194eb30d75f19183016d4ba AS kairos-init

FROM almalinux:10@sha256:a81adef1de0ec017d2f483e6acedf6da4c060a93716192c23cac052386e48035 AS base-kairos

# renovate: datasource=github-releases depName=kairos-io/kairos-framework
ARG FRAMEWORK_VERSION=v2.21.0
ARG KUBERNETES_DISTRO=k3s
# renovate: datasource=custom.k3s depName=k3s
ARG KUBERNETES_VERSION=v1.32.3+k3s1
ARG MODEL=generic
ARG TRUSTED_BOOT=false
ARG VERSION

COPY --from=kairos-init /kairos-init /kairos-init

# hadolint ignore=DL3059
RUN /kairos-init \
  -s install \
  --fips \
  -k "${KUBERNETES_DISTRO}" \
  --k8sversion "${KUBERNETES_VERSION}" \
  -l debug \
  -m "${MODEL}" \
  -t "${TRUSTED_BOOT}" \
  --version "${VERSION}"

# hadolint ignore=DL3041
RUN dnf update && dnf install -y \
  nmstate \
  python3-pip && \
  dnf clean all

RUN curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 && \
  chmod 700 get_helm.sh && \
  HELM_INSTALL_DIR=/usr/bin VERIFY_CHECKSUM=false ./get_helm.sh && \
  rm get_helm.sh

# hadolint ignore=DL3059
RUN /kairos-init \
  -s init \
  --fips \
  -k "${KUBERNETES_DISTRO}" \
  --k8sversion "${KUBERNETES_VERSION}" \
  -l debug \
  -m "${MODEL}" \
  -t "${TRUSTED_BOOT}" \
  --version "${VERSION}"

# hadolint ignore=DL3059
RUN /kairos-init validate -t "${TRUSTED_BOOT}"

# hadolint ignore=DL3059
RUN rm /kairos-init
