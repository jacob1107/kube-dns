# 使用 Ubuntu 20.04 官方镜像作为基础
FROM ubuntu:20.04

# 更新软件源并安装所需工具（非交互模式）
RUN apt-get update && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y \
    wget \
    curl \
    s3cmd \
    && rm -rf /var/lib/apt/lists/*

# 可选：设置默认工作目录
WORKDIR /app
