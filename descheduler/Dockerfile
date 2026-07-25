# syntax=docker/dockerfile:1.7
ARG CUDA_VER=12.8.1
ARG UB_VER=22.04

# ---------- Stage 1: builder ----------
FROM nvidia/cuda:12.8.1-devel-ubuntu22.04 AS builder
ENV DEBIAN_FRONTEND=noninteractive
ENV PATH=/usr/local/go/bin:$PATH

RUN apt-get update && apt-get install -y --no-install-recommends \
    git cmake build-essential libtool autoconf automake pkg-config \
    libssl-dev ca-certificates curl ninja-build \
    python3 python3-dev python3-pip \
    && rm -rf /var/lib/apt/lists/*

# Go（同你原逻辑）
RUN curl -L https://dl.google.com/go/go1.22.6.linux-amd64.tar.gz -o go.tar.gz \
    && tar -C /usr/local -xzf go.tar.gz \
    && rm go.tar.gz

WORKDIR /workspace
RUN git clone --depth 1 --branch v0.3.12 https://github.com/kvcache-ai/Mooncake.git

RUN cd Mooncake \
    && bash dependencies.sh \
    && mkdir -p build && cd build \
    && cmake .. -G Ninja \
        -DCMAKE_BUILD_TYPE=Release \
        -DWITH_TE=ON \
        -DWITH_STORE=OFF \
        -DWITH_STORE_RUST=OFF \
        -DBUILD_EXAMPLES=ON \
        -DUSE_CUDA=ON \
        -DUSE_MNNVL=ON \
        -DUSE_TENT=ON \
        -DUSE_ETCD=ON \
        -DUSE_INTRA_NVLINK=ON \
        -DUSE_TCP=ON \
        -DUSE_HTTP=ON \
    && cmake --build . --target install

# 顺手把 python 包也打成 wheel（可选，但推荐）
RUN cd /workspace/Mooncake && ./scripts/build_wheel.sh 3.10 dist || true

# ---------- Stage 2: runtime ----------
FROM nvidia/cuda:12.8.1-devel-ubuntu22.04 AS runtime
ENV DEBIAN_FRONTEND=noninteractive
ENV LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH

RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates curl \
    libssl3 \
    libibverbs1 librdmacm1 libnuma1 liburing2 libyaml-0-2 libcurl4 \
    python3 python3-pip \
    && rm -rf /var/lib/apt/lists/*

# 只搬运行必需物
COPY --from=builder /usr/local/lib/python3.10/dist-packages/mooncake /usr/local/lib/python3.10/dist-packages/mooncake
COPY --from=builder /usr/local/lib/libmooncake* /usr/local/lib/
COPY --from=builder /usr/local/bin/mooncake* /usr/local/bin/ 2>/dev/null || true

# 如果你用了 wheel，也可以改成：
# COPY --from=builder /workspace/Mooncake/dist/*.whl /tmp/
# RUN pip3 install --no-cache-dir /tmp/*.whl && rm -f /tmp/*.whl

CMD ["bash"]
