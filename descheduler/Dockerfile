# 使用官方最小化构建 (focal-20230624)
FROM ubuntu:noble-20250127
# 单层构建减少镜像体积
RUN export DEBIAN_FRONTEND=noninteractive && \
    # 配置时区避免交互提示
    ln -fs /usr/share/zoneinfo/UTC /etc/localtime && \
    # 组合所有操作减少层体积
    apt-get update -qqy \
    && apt-get install -y --no-install-recommends \
        ca-certificates \
        wget \
        curl \
        s3cmd \
        tar \
        zip \
    # 深度清理
    && apt-get clean -y \
    && apt-get autoremove -y \
    && rm -rf \
        /var/lib/apt/lists/* \
        /var/cache/apt/archives/*.deb \
        /var/cache/apt/*.bin \
        /var/log/alternatives.log \
        /var/log/dpkg.log \
        /tmp/* \
        /var/tmp/*
