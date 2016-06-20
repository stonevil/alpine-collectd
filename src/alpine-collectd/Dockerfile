FROM quay.io/stonevil/alpine-base
MAINTAINER stonevil@gmail.com

ENV COLLECTD_VERSION=${COLLECTD_VERSION:-5.5.1-r0}

LABEL com.stonevil.collectd.version=${COLLECTD_VERSION}
  com.stonevil.k8s.ready=true

RUN ${APK_UPGRADE_CMD} && \
  ${APK_INSTALL_CMD} collectd=${COLLECTD_VERSION} collectd-dev=${COLLECTD_VERSION} && \
  ${APK_CACHE_RM}

ADD _rootfs /
ONBUILD ADD _rootfs_vendor /

VOLUME ["/data/collectd"]

ENV INFLUXDB_OUTPUT_INIT=${INFLUXDB_OUTPUT_INIT:-true}
ENV INFLUXDB_HOST=${INFLUXDB_HOST:-localhost}
ENV INFLUXDB_PORT=${INFLUXDB_PORT:-25826}

CMD mkdir -p /data/collectd && chown -Rf collectd /data/collectd && \
  if ${INFLUXDB_OUTPUT_INIT}; then \
  (sed -i -e 's|_INFLUXDBHOST_|'"${INFLUXDB_HOST}"'|g' -e 's|_INFLUXDBPORT__|'"${INFLUXDB_PORT}"'|g' /etc/collectd/collectd.conf && \
  echo 'Include "/etc/collectd/conf.d/influxdb.conf"' >> /etc/collectd/collectd.conf); fi && \
  gosu collectd collectd -f -t -C /etc/collectd/collectd.conf && \
  gosu collectd collectd -f -C /etc/collectd/collectd.conf
