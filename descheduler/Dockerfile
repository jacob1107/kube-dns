

ARG CUDA_VERSION=12.8.1
ARG UBUNTU_VERSION=22.04
ARG PYTHON_VERSION=3.12

FROM nvidia/cuda:${CUDA_VERSION}-devel-ubuntu${UBUNTU_VERSION} AS builder
ENV DEBIAN_FRONTEND=noninteractive \
    PYTHONUNBUFFERED=1 \
    PATH="/usr/local/go/bin:${PATH}"

# ---- 基础工具 + Python ----
RUN apt-get update && apt-get install -y --no-install-recommends \
    ca-certificates curl git ninja-build pkg-config \
    software-properties-common build-essential cmake \
    python${PYTHON_VERSION} python${PYTHON_VERSION}-dev python${PYTHON_VERSION}-venv \
    libibverbs-dev libcurl4-openssl-dev liburing-dev libjemalloc-dev \
    libhiredis-dev libnl-3-dev libnl-route-3-dev numactl \
    && add-apt-repository -y ppa:deadsnakes/ppa \
    && apt-get update && apt-get install -y --no-install-recommends python${PYTHON_VERSION} \
    && curl -sS https://bootstrap.pypa.io/get-pip.py | python${PYTHON_VERSION} \
    && update-alternatives --install /usr/bin/python python /usr/bin/python${PYTHON_VERSION} 1 \
    && update-alternatives --install /usr/bin/python3 python3 /usr/bin/python${PYTHON_VERSION} 1 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /workspace
RUN git clone --depth 1 --branch v0.3.12 https://github.com/kvcache-ai/Mooncake.git . \
    && git fetch --tags && git checkout v0.3.12

# ---- 装 yalantinglibs / Go / etcd_wrapper 依赖 ----
RUN bash dependencies.sh -y

# ---- 编译（TENT + CUDA + ETCD + Store etdc） ----
RUN mkdir -p build && cd build \
    && cmake -G Ninja .. \
       -DCMAKE_BUILD_TYPE=Release \
       -DUSE_CUDA=ON \
       -DUSE_TENT=ON \
       -DUSE_ETCD=ON \
       -DSTORE_USE_ETCD=ON \
       -DUSE_HTTP=ON \
       -DBUILD_UNIT_TESTS=OFF \
       -DBUILD_SHARED_LIBS=ON \
       -DPython3_EXECUTABLE=/usr/bin/python${PYTHON_VERSION} \
    && export LIBRARY_PATH=/usr/local/cuda/lib64/stubs:$LIBRARY_PATH \
    && cmake --build . -j$(nproc)
CMD ["bash"]
