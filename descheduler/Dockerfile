FROM nvidia/cuda:12.8.1-devel-ubuntu22.04


RUN apt-get update && apt-get install -y -git  cmake 

WORKDIR /workspace
RUN  git clone --depth 1 --branch v0.3.12 https://github.com/kvcache-ai/Mooncake.git
RUN  cd Mooncake
  && bash dependencies.sh
  && mkdir -p build && cd build
RUN cmake .. -DUSE_CUDA=ON -DUSE_TENT=ON
 && make -j$(nproc)

RUN cp build/mooncake-transfer-engine/example/tebench /usr/local/bin/tebench \
    && cp build/mooncake-transfer-engine/example/transfer_engine_bench /usr/local/bin/transfer_engine_bench

WORKDIR /workspace
CMD ["bash"]
