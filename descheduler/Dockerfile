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
    && cmake .. -DUSE_CUDA=ON -DUSE_TENT=ON
    && make -j$(nproc) 

RUN cp build/mooncake-transfer-engine/example/tebench /usr/local/bin/tebench \
    && cp build/mooncake-transfer-engine/example/transfer_engine_bench /usr/local/bin/transfer_engine_bench

WORKDIR /workspace
CMD ["bash"]
