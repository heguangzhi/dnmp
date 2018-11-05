FROM memcached:1.5.11-alpine

#修改时区
USER root
RUN apk update && apk add curl bash tree tzdata \
    && cp -r -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime \
    && apk del tzdata



USER memcache
EXPOSE 11211
CMD ["memcached"]