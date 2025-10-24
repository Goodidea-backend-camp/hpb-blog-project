ARG NGINX_VERSION
FROM ${NGINX_VERSION}

# 設定預設環境字集
ENV LANG C.UTF-8

# Install logrotate
# RUN 指令可以協助安裝套件或者執行否些指令
RUN apk update && apk add --no-cache logrotate nano