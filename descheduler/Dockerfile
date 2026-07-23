
FROM nvidia/cuda:12.4.1-devel-ubuntu22.04

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    git cmake g++ gcc build-essential \
    libibverbs-dev libnuma-dev libgoogle-glog-dev \
    libgflags-dev libjsoncpp-dev libcurl4-openssl-dev \
    libssl-dev libhiredis-dev patchelf wget unzip \
    python3 python3-pip && rm -rf /var/lib/apt/lists/*

WORKDIR /opt
RUN git clone https://github.com/kvcache-ai/Mooncake.git \
 && cd Mooncake \
 && git checkout v0.3.11 \
 && git submodule update --init --recursive \
 && bash dependencies.sh || true

WORKDIR /opt/Mooncake/build
RUN cmake .. \
      -DCMAKE_BUILD_TYPE=Release \
      -DWITH_TE=ON \
      -DWITH_STORE=OFF \
      -DWITH_STORE_RUST=OFF \
      -DBUILD_EXAMPLES=ON \
      -DUSE_CUDA=ON \
      -DUSE_MNNVL=ON \
      -DUSE_INTRA_NVLINK=ON \
      -DUSE_TCP=ON \
      -DUSE_HTTP=ON \
 && make -j$(nproc) \
 && make install

ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
ENTRYPOINT ["bash"]   
