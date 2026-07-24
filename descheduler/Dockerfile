FROM nvidia/cuda:12.8.1-devel-ubuntu22.04


RUN apt-get update && apt-get install -y git  cmake 

WORKDIR /workspace
RUN  git clone --depth 1 --branch v0.3.12 https://github.com/kvcache-ai/Mooncake.git
RUN  cd Mooncake && bash dependencies.sh \
  && mkdir -p build && cd build
  
RUN  cmake .. \
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
 && make -j$(nproc) \
 && make install

RUN cp build/mooncake-transfer-engine/example/tebench /usr/local/bin/tebench \
    && cp build/mooncake-transfer-engine/example/transfer_engine_bench /usr/local/bin/transfer_engine_bench

WORKDIR /workspace
CMD ["bash"]
