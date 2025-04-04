FROM alpine:latest AS builder

ARG USER=WJQSERVER-STUDIO
ARG REPO=ghproxy
ARG APPLICATION=ghproxy
ARG TARGETOS
ARG TARGETARCH
ARG TARGETPLATFORM

# 创建文件夹
RUN mkdir -p /data/www
RUN mkdir -p /data/${APPLICATION}/config 
RUN mkdir -p /data/${APPLICATION}/log 

# 安装依赖
RUN apk add --no-cache curl wget tar

# 后端
RUN VERSION=$(curl -s https://raw.githubusercontent.com/${USER}/${REPO}/dev/DEV-VERSION) && \
    wget -O /data/${APPLICATION}/${APPLICATION}-${TARGETOS}-${TARGETARCH}.tar.gz https://github.com/${USER}/${REPO}/releases/download/$VERSION/${APPLICATION}-${TARGETOS}-${TARGETARCH}.tar.gz && \
    tar -zxvf /data/${APPLICATION}/${APPLICATION}-${TARGETOS}-${TARGETARCH}.tar.gz -C /data/${APPLICATION} && \
    rm -rf /data/${APPLICATION}/${APPLICATION}-${TARGETOS}-${TARGETARCH}.tar.gz
RUN wget -O /usr/local/bin/init.sh https://raw.githubusercontent.com/${USER}/${REPO}/dev/docker/dockerfile/dev/init.sh

# 拉取配置
#RUN wget -O /data/caddy/Caddyfile https://raw.githubusercontent.com/${USER}/${REPO}/dev/caddyfile/dev/Caddyfile
RUN wget -O /data/${APPLICATION}/config.toml https://raw.githubusercontent.com/${USER}/${REPO}/dev/config/config.toml
RUN wget -O /data/${APPLICATION}/blacklist.json https://raw.githubusercontent.com/${USER}/${REPO}/dev/config/blacklist.json
RUN wget -O /data/${APPLICATION}/whitelist.json https://raw.githubusercontent.com/${USER}/${REPO}/dev/config/whitelist.json

# 权限
RUN chmod +x /data/${APPLICATION}/${APPLICATION}
RUN chmod +x /usr/local/bin/init.sh

FROM alpine:latest

RUN apk add --no-cache curl

COPY --from=builder /data/www /data/www
COPY --from=builder /data/${APPLICATION} /data/${APPLICATION}
COPY --from=builder /usr/local/bin/init.sh /usr/local/bin/init.sh

# 权限
RUN chmod +x /data/${APPLICATION}/${APPLICATION}
RUN chmod +x /usr/local/bin/init.sh

CMD ["/usr/local/bin/init.sh"]