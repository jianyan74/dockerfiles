ARG MONGO_VER

FROM mongo:${MONGO_VER}

# 设置时区
ENV TZ=Asia/Shanghai
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

COPY mongod.conf /etc/mongo/mongod.conf
CMD ["mongod", "-f", "/etc/mongo/mongod.conf"]