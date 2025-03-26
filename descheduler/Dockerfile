# 使用官方最小化构建 (focal-20230624)
FROM ubuntu@sha256:7a6a5c760ef7b8fe87acfef691b5b0c7b7e88d82b679c8a1c7b0c0aec7aadf3b

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
