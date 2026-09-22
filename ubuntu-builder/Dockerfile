FROM ubuntu:24.04

ARG LLVM_VERSION=22
ARG CMAKE_VERSION=4.4.2

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates curl gnupg lsb-release wget software-properties-common \
        git ninja-build libc6-dev libstdc++-14-dev findutils \
    && rm -rf /var/lib/apt/lists/*

# clang/llvm from apt.llvm.org. Using the packaged clang-tidy (instead of the
# static binaries) means it ships with its resource dir (stddef.h etc.)
RUN wget -qO /tmp/llvm.sh https://apt.llvm.org/llvm.sh \
    && chmod +x /tmp/llvm.sh \
    && /tmp/llvm.sh ${LLVM_VERSION} \
    && apt-get install -y --no-install-recommends \
        clang-tidy-${LLVM_VERSION} clang-format-${LLVM_VERSION} \
        libc++-${LLVM_VERSION}-dev libc++abi-${LLVM_VERSION}-dev \
    && rm -rf /var/lib/apt/lists/* /tmp/llvm.sh \
    && for t in clang clang++ clang-tidy clang-format; do \
        ln -sf /usr/bin/$t-${LLVM_VERSION} /usr/local/bin/$t; \
    done

# cmake v4 from kitware release tarball
RUN curl -fsSL "https://github.com/Kitware/CMake/releases/download/v${CMAKE_VERSION}/cmake-${CMAKE_VERSION}-linux-x86_64.tar.gz" \
        | tar -xz -C /opt \
    && ln -sf /opt/cmake-${CMAKE_VERSION}-linux-x86_64/bin/* /usr/local/bin/

# go-task
RUN sh -c "$(curl -fsSL https://taskfile.dev/install.sh)" -- -d -b /usr/local/bin
