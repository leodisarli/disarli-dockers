FROM clearlinux:latest AS builder

ARG swupd_args
RUN swupd update --no-boot-update $swupd_args

COPY --from=clearlinux/os-core:latest /usr/lib/os-release /

RUN source /os-release && \
    mkdir /install_root \
    && swupd os-install -V ${VERSION_ID} \
    --path /install_root --statedir /swupd-state \
    --bundles=nginx-mainline --no-boot-update

RUN mkdir /os_core_install
COPY --from=clearlinux/os-core:latest / /os_core_install/
RUN cd / && \
    find os_core_install | sed -e 's/os_core_install/install_root/' | xargs rm -d &> /dev/null || true

FROM clearlinux/os-core:latest

COPY --from=builder /install_root /
COPY nginx.conf /etc/nginx-mainline/
COPY mime.types /usr/share/nginx-mainline/conf/mime.types

RUN mkdir -p /var/www/html && \
    mkdir -p /etc/nginx-mainline && \
    mkdir -p /var/log/nginx-mainline && \
    mkdir -p /var/lib/nginx-mainline/uwsgi && \
    mkdir -p /var/lib/nginx-mainline/client-body && \
    mkdir -p /var/lib/nginx-mainline/proxy && \
    mkdir -p /var/lib/nginx-mainline/fast-cgi && \
    ln -sf /var/log/nginx-mainline /var/log/nginx && \
    ln -sf /var/lib/nginx-mainline /var/lib/nginx && \
    ln -sf /etc/nginx-mainline /etc/nginx && \
    ln -sf /usr/bin/nginx-mainline /usr/bin/nginx && \
    ln -sf /dev/stdout /var/log/nginx-mainline/access.log && \
    ln -sf /dev/stderr /var/log/nginx-mainline/error.log && \
    cp -f /usr/share/nginx-mainline/html/* /var/www/html/

EXPOSE 80
EXPOSE 443

STOPSIGNAL SIGTERM

CMD ["nginx", "-g", "daemon off;"]
