FROM nvidia/cuda:12.8.1-devel-ubuntu22.04

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y \
    git cmake 
WORKDIR /opt
RUN git clone https://github.com/kvcache-ai/Mooncake.git \
 && cd Mooncake \
 && git checkout v0.3.12 \
 && bash dependencies.sh 
 
RUN mkdir -p build  \
 && cd build \
&& cmake .. -DUSE_TENT=ON \
&& cmake --build . -j 

ENV LD_LIBRARY_PATH=/usr/local/cuda/lib64:$LD_LIBRARY_PATH
ENTRYPOINT ["bash"]   
