FROM clearlinux:latest AS builder

ARG swupd_args

RUN swupd update --no-boot-update $swupd_args

COPY --from=clearlinux/os-core:latest /usr/lib/os-release /

RUN source /os-release && \
    mkdir /install_root \
    && swupd os-install -V ${VERSION_ID} \
    --path /install_root --statedir /swupd-state \
    --bundles=os-core-update,php-basic-dev,curl,wget --no-boot-update

RUN mkdir /os_core_install

COPY --from=clearlinux/os-core:latest / /os_core_install/

RUN cd / && \
    find os_core_install | sed -e 's/os_core_install/install_root/' | xargs rm -d &> /dev/null || true


FROM clearlinux/os-core:latest

COPY --from=builder /install_root /

RUN set -ex \
	mkdir -p /var/www/html \
	&& cd /usr/share/defaults/php \
	&& { \
		echo '[global]'; \
		echo 'error_log = /proc/self/fd/2'; \
		echo; echo '; https://github.com/docker-library/php/pull/725#issuecomment-443540114'; echo 'log_limit = 8192'; \
		echo; \
		echo '[www]'; \
		echo '; if we send this to /proc/self/fd/1, it never appears'; \
		echo 'access.log = /proc/self/fd/2'; \
		echo; \
		echo 'clear_env = no'; \
		echo; \
		echo '; Ensure worker stdout and stderr are sent to the main error log.'; \
		echo 'catch_workers_output = yes'; \
		echo 'decorate_workers_output = no'; \
	} | tee php-fpm.d/docker.conf \
	&& { \
		echo '[global]'; \
		echo 'daemonize = no'; \
		echo; \
		echo '[www]'; \
        echo 'user = httpd'; \
        echo 'group = httpd'; \
		echo 'listen = 9000'; \
        echo 'pm = dynamic'; \
	} | tee php-fpm.d/zz-docker.conf

RUN mkdir /temp \
    && cd temp \
    && wget http://xdebug.org/files/xdebug-2.7.2.tgz \
    && tar -xvzf xdebug-2.7.2.tgz \
    && cd xdebug-2.7.2 \
    && phpize \
    && ./configure \
    && make \
    && cp modules/xdebug.so /usr/lib64/extensions/no-debug-non-zts-20180731 \
    && echo 'zend_extension=/usr/lib64/extensions/no-debug-non-zts-20180731/xdebug.so' >> /usr/share/defaults/php/php.ini \
    && cd / \
    && rm -rf temp

WORKDIR /var/www/html

COPY docker-php-entrypoint /usr/local/bin/

RUN chmod +x /usr/local/bin/docker-php-entrypoint

ENTRYPOINT ["docker-php-entrypoint"]

STOPSIGNAL SIGQUIT

EXPOSE 9000

CMD ["php-fpm"]
