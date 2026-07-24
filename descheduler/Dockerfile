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
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# ========== 2. 【关键】彻底清理 NVIDIA 镜像自带的 Go ==========
# nvidia/cuda 镜像自带 Go，会导致 GOROOT 污染（internal/abi 冲突）
RUN rm -rf \
    /usr/local/go \
    /usr/lib/go-* \
    /usr/local/go-* \
    /usr/share/go \
    /usr/bin/go \
    /etc/alternatives/go \
    /var/lib/dpkg/info/golang*.list

# ========== 3. 安装官方 Go（>= 1.22，匹配 Mooncake etcd） ==========
ENV GO_VERSION=1.22.5
RUN wget -O go.tar.gz https://dl.google.com/go/go${GO_VERSION}.linux-amd64.tar.gz \
    && tar -C /usr/local -xzf go.tar.gz \
    && rm go.tar.gz

ENV PATH=/usr/local/go/bin:$PATH
ENV GOROOT=/usr/local/go

# ========== 4. 验证 Go 环境（调试用，构建成功后可选删除） ==========
RUN go version && go env GOROOT

# ========== 5. 克隆 Mooncake 并初始化子模块 ==========
WORKDIR /opt
RUN git clone https://github.com/kvcache-ai/Mooncake.git \
 && cd Mooncake \
 && git checkout v0.3.11 \
 && git submodule update --init --recursive

# ========== 6. 依赖准备（失败即退出，禁止 || true） ==========
WORKDIR /opt/Mooncake
RUN bash dependencies.sh

# ========== 7. CMake 配置 ==========
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

# ========== 8. 【关键】单线程构建 etcd_wrapper，避免吞错误 ==========
RUN make VERBOSE=1 -j1 build_etcd_wrapper

# ========== 9. 并行构建其余组件 ==========
RUN make -j$(nproc) \
 && make install

# ========== 10. 运行时库路径 ==========
ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:/usr/local/lib:$LD_LIBRARY_PATH

ENTRYPOINT ["bash"]
