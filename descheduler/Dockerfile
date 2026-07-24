FROM nvidia/cuda:12.9.1-devel-ubuntu22.04

ENV DEBIAN_FRONTEND=noninteractive

# ========== 1. 基础系统与编译工具 ==========
RUN apt-get update && apt-get install -y \
    git \
    cmake \
    g++ \
    gcc \
    build-essential \
    pkg-config \
    libibverbs-dev \
    libnuma-dev \
    libgoogle-glog-dev \
    libgflags-dev \
    libjsoncpp-dev \
    libcurl4-openssl-dev \
    rdma-core \
    ibverbs-providers \
    infiniband-diags \
    ibverbs-utils \
    libssl-dev \
    libhiredis-dev \
    patchelf \
    wget \
    unzip \
    python3 \
    python3-pip \
    && rm -rf /var/lib/apt/lists/*

# ========== 2. 清理系统自带 Go ==========
RUN apt-get remove -y golang golang-go || true \
    && rm -rf /usr/local/go /usr/lib/go

# ========== 3. 安装官方 Go（>= 1.22，匹配 Mooncake etcd） ==========
ENV GO_VERSION=1.22.5
RUN wget -O go.tar.gz https://dl.google.com/go/go${GO_VERSION}.linux-amd64.tar.gz \
    && tar -C /usr/local -xzf go.tar.gz \
    && rm go.tar.gz

ENV PATH=/usr/local/go/bin:$PATH
ENV GOROOT=/usr/local/go

# ========== 4. 克隆 Mooncake 并初始化子模块 ==========
WORKDIR /opt
RUN git clone https://github.com/kvcache-ai/Mooncake.git \
 && cd Mooncake \
 && git checkout v0.3.11 \
 && git submodule update --init --recursive

# ========== 5. 运行依赖脚本（失败即退出，关键！） ==========
WORKDIR /opt/Mooncake
RUN bash dependencies.sh

# ========== 6. 构建 etcd_wrapper（单线程 + 详细日志，核心修复点） ==========
WORKDIR /opt/Mooncake/build
RUN cmake .. \
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
      -DUSE_HTTP=ON

# 👇 这是你之前一直缺的一步
RUN make VERBOSE=1 -j1 build_etcd_wrapper

# ========== 7. 并行构建其余组件 ==========
RUN make -j$(nproc) \
 && make install

# ========== 8. 运行时库路径 ==========
ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/lib:$LD_LIBRARY_PATH

ENTRYPOINT ["bash"]
