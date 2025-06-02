FROM quay.io/kairos/kairos-init:v0.4.7@sha256:fc9564833491c504e30f5894dfab3c6fd394fe6f02491553722ff9cb10fedd6e AS kairos-init

FROM almalinux:9@sha256:e909867582ddb6256b90113dd3de19150b968a565affbd898b9f0db358c2d0ab AS base-kairos

# renovate: datasource=github-releases depName=kairos-io/kairos-framework
ARG FRAMEWORK_VERSION=v2.21.0
ARG KUBERNETES_DISTRO=k3s
# renovate: datasource=custom.k3s depName=k3s
ARG KUBERNETES_VERSION=v1.32.3+k3s1
ARG MODEL=generic
ARG REGISTRY=ghcr.io/ucl-arc
ARG TRUSTED_BOOT=false
ARG VARIANT=standard
ARG VERSION

COPY --from=kairos-init /kairos-init /kairos-init

# hadolint ignore=DL3059
RUN /kairos-init \
  -s install \
  -f "${FRAMEWORK_VERSION}" \
  --fips \
  -k "${KUBERNETES_DISTRO}" \
  --k8sversion "${KUBERNETES_VERSION}" \
  -l debug \
  -m "${MODEL}" \
  -r "${REGISTRY}" \
  -t "${TRUSTED_BOOT}" \
  -v "${VARIANT}" \
  --version "${VERSION}"

RUN dnf update && dnf install -y \
  python3-pip && \
  dnf clean all

RUN curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 && \
  chmod 700 get_helm.sh && \
  HELM_INSTALL_DIR=/usr/bin ./get_helm.sh && \
  rm get_helm.sh

# hadolint ignore=DL3059
RUN /kairos-init \
  -s init \
  -f "${FRAMEWORK_VERSION}" \
  --fips \
  -k "${KUBERNETES_DISTRO}" \
  --k8sversion "${KUBERNETES_VERSION}" \
  -l debug \
  -m "${MODEL}" \
  -r "${REGISTRY}" \
  -t "${TRUSTED_BOOT}" \
  -v "${VARIANT}" \
  --version "${VERSION}"

# hadolint ignore=DL3059
RUN /kairos-init \
  --validate \
  -f "${FRAMEWORK_VERSION}" \
  --fips \
  -k "${KUBERNETES_DISTRO}" \
  --k8sversion "${KUBERNETES_VERSION}" \
  -l debug \
  -m "${MODEL}" \
  -r "${REGISTRY}" \
  -t "${TRUSTED_BOOT}" \
  -v "${VARIANT}" \
  --version "${VERSION}"

# hadolint ignore=DL3059
RUN rm /kairos-init
