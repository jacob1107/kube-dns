FROM nvidia/cuda:12.8.1-devel-ubuntu22.04


RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates curl git ninja-build cmake \
    build-essential python3 python3-dev python3-pip \
    libibverbs-dev librdmacm-dev rdma-core ibverbs-providers \
    libcurl4-openssl-dev liburing-dev libjemalloc-dev \
    libhiredis-dev libnl-3-dev libnl-route-3-dev numactl   \
    etcd-server \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace
RUN git clone --depth 1 --branch v0.3.12 https://github.com/kvcache-ai/Mooncake.git . \
    && git fetch --tags && git checkout v0.3.12

RUN bash dependencies.sh -y

# ---- 编译：TENT(NVLink) + CUDA(RDMA/GDR) + ETCD ----
RUN mkdir -p build && cd build \
    && cmake  .. \
       -DCMAKE_BUILD_TYPE=Release \
       -DUSE_CUDA=ON \
       -DUSE_TENT=ON \
       -DUSE_INTRA_NVLINK=ON \
       -DUSE_ETCD=ON \
       -DSTORE_USE_ETCD=ON \
       -DUSE_HTTP=ON \
       -DUSE_TCP=ON \
       -DBUILD_UNIT_TESTS=OFF \
       -DBUILD_SHARED_LIBS=ON \
       -DPython3_EXECUTABLE=/usr/bin/python3 \
    && cmake --build . -j$(nproc)

RUN cp build/mooncake-transfer-engine/example/tebench /usr/local/bin/tebench \
    && cp build/mooncake-transfer-engine/example/transfer_engine_bench /usr/local/bin/transfer_engine_bench

WORKDIR /workspace
CMD ["bash"]
