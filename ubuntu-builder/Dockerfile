FROM ubuntu:24.04

ARG LLVM_VERSION=22
ARG NX_AARCH64=22.1.8-1
ARG CMAKE_VERSION=4.4.2

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates curl \
        git ninja-build findutils \
        python3 python-is-python3 \
    && rm -rf /var/lib/apt/lists/*

# the checkout in CI is owned by a different uid than the container user,
# which makes git refuse to treat it as a repo, this suppresses that
RUN git config --system --add safe.directory '*'

# clang-format and clang-tidy binaries
RUN curl -fsSL "https://github.com/cpp-linter/clang-tools-static-binaries/releases/latest/download/clang-format-22_linux-amd64" -o "/usr/bin/clang-format" \
    && curl -fsSL "https://github.com/cpp-linter/clang-tools-static-binaries/releases/latest/download/clang-tidy-22_linux-amd64" -o "/usr/bin/clang-tidy" \
    && chmod +x "/usr/bin/clang-format" "/usr/bin/clang-tidy"

# Mini nx-aarch64 toolchain and musl
RUN mkdir /opt/nx \
    && curl -fsSL https://raw.githubusercontent.com/Pistonight/nx-aarch64/main/install.sh | VERSION="${NX_AARCH64}" OUTPUT="/opt/nx" sh \
    && git clone https://github.com/open-ead/botw-lib-musl /opt/nx/musl
ENV NX_MUSL=/opt/nx/musl
ENV NX_CLANG=/opt/nx/nx-aarch64-${NX_AARCH64}

# cmake v4 from kitware release tarball
RUN curl -fsSL "https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-x86_64.tar.gz" \
        | tar -xz -C /opt \
    && ln -sf /opt/cmake-${CMAKE_VERSION}-linux-x86_64/bin/* /usr/local/bin/

# go-task
RUN sh -c "$(curl -fsSL https://taskfile.dev/install.sh)" -- -d -b /usr/local/bin


