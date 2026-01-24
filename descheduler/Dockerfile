FROM swr.cn-north-4.myhuaweicloud.com/ddn-k8s/docker.io/python:3.10.4

# 合并APT操作到单个RUN指令减少镜像层
RUN sed -i 's/deb.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list && \
    sed -i 's/security.debian.org/mirrors.aliyun.com/g' /etc/apt/sources.list && \
    apt-get update && \
    apt-get install -y --no-install-recommends tzdata vim && \
    ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    rm -rf /var/lib/apt/lists/*

# 使用多行EOF语法提高可读性
RUN cat > ~/.vimrc <<-'EOF'
    set nocompatible
    set backspace=indent,eol,start
    syntax on
    set mouse=
EOF

# 使用HTTPS镜像源并合并pip操作
RUN pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple && \
    pip install --no-cache-dir   "evalscope[all]"==1.4.2

