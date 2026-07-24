FROM nvidia/cuda:12.8.1-devel-ubuntu22.04


RUN apt-get update && apt-get install -y -git  cmake 

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
