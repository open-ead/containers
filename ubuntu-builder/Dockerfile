FROM ubuntu:24.04

ARG LLVM_VERSION=22
ARG CMAKE_VERSION=4.4.2

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates curl \
        git ninja-build libc6-dev libstdc++-14-dev findutils \
        python3 python-is-python3 \
    && rm -rf /var/lib/apt/lists/*

# the checkout in CI is owned by a different uid than the container user,
# which makes git refuse to treat it as a repo, this suppresses that
RUN git config --system --add safe.directory '*'

# clang/llvm from apt.llvm.org. Using the packaged clang-tidy (instead of the
# static binaries) means it ships with its resource dir (stddef.h etc.)
# The repo is added by hand instead of using llvm.sh, since that installs the
# whole toolchain (lldb, lld, clangd...) and needs software-properties-common
RUN . /etc/os-release \
    && curl -fsSL https://apt.llvm.org/llvm-snapshot.gpg.key -o /etc/apt/keyrings/llvm.asc \
    && echo "deb [signed-by=/etc/apt/keyrings/llvm.asc] http://apt.llvm.org/${VERSION_CODENAME}/ llvm-toolchain-${VERSION_CODENAME}-${LLVM_VERSION} main" \
        > /etc/apt/sources.list.d/llvm.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        clang-${LLVM_VERSION} clang-tidy-${LLVM_VERSION} clang-format-${LLVM_VERSION} \
        libc++-${LLVM_VERSION}-dev libc++abi-${LLVM_VERSION}-dev \
    && rm -rf /var/lib/apt/lists/* \
    && for t in clang clang++ clang-tidy clang-format; do \
        ln -sf /usr/bin/$t-${LLVM_VERSION} /usr/local/bin/$t; \
    done

# cmake v4 from kitware release tarball
RUN curl -fsSL "https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-x86_64.tar.gz" \
        | tar -xz -C /opt \
    && ln -sf /opt/cmake-${CMAKE_VERSION}-linux-x86_64/bin/* /usr/local/bin/

# go-task
RUN sh -c "$(curl -fsSL https://taskfile.dev/install.sh)" -- -d -b /usr/local/bin