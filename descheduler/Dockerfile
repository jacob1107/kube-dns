FROM nvidia/cuda:12.8.1-devel-ubuntu22.04

# 基础依赖（合并安装，减少层数）
RUN apt-get update && apt-get install -y --no-install-recommends \
    git \
    cmake \
    build-essential \
    libtool \
    autoconf \
    automake \
    pkg-config \
    libssl-dev \
    ca-certificates \
    curl \
    && rm -rf /var/lib/apt/lists/*

RUN curl -L https://dl.google.com/go/go1.25.9.linux-amd64.tar.gz -o go.tar.gz \
    && tar -C /usr/local -xzf go.tar.gz \
    && rm go.tar.gz \
    && ln -sf /usr/local/go/bin/go /usr/bin/go && go  version 
    
WORKDIR /workspace

# 拉取 Mooncake 源码
RUN git clone --depth 1 --branch v0.3.12 https://github.com/kvcache-ai/Mooncake.git

# 安装依赖并创建 build 目录（同一 RUN 内完成）
RUN cd Mooncake \
    && bash dependencies.sh \
    && mkdir -p build \
    && cd build \
    && cmake .. \
        -DCMAKE_BUILD_TYPE=Release \
        -DWITH_TE=ON \
        -DWITH_STORE=OFF \
        -DWITH_STORE_RUST=OFF \
        -DBUILD_EXAMPLES=ON \
        -DUSE_CUDA=ON \
        -DUSE_MNNVL=ON \
        -DUSE_TENT=ON \
        -DUSE_INTRA_NVLINK=ON \
        -DUSE_TCP=ON \
        -DUSE_HTTP=ON \
    && make -j2 \
    && make install 
