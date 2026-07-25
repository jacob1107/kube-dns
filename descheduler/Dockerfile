FROM nvidia/cuda:12.8.1-devel-ubuntu22.04

RUN apt-get update && apt-get install -y     git cmake 


RUN git clone https://github.com/kvcache-ai/Mooncake.git \
 && cd Mooncake \
 && git checkout v0.3.12 \
 && bash dependencies.sh 
 
RUN mkdir -p build  \
 && cd build \
&& cmake .. -DUSE_TENT=ON \
&& cmake --build . -j 

ENTRYPOINT ["bash"]   
