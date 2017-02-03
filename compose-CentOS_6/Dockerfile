FROM centos:6
VOLUME /sys/fs/cgroup /run /tmp
ENV container=docker
RUN echo $'[influxdb] \n\
name = InfluxDB Repository - RHEL \$releasever \n\
baseurl = https://repos.influxdata.com/rhel/\$releasever/\$basearch/stable \n\
enabled = 1 \n\
gpgcheck = 1 \n\
gpgkey = https://repos.influxdata.com/influxdb.key \n'\
> /etc/yum.repos.d/influxdb.repo \
    && yum clean all \
    && yum -y update \
    && yum install -y initscripts \
    && yum install -y influxdb \
    && yum install -y httpd mod-ssl \
    && yum install -y epel-release \
    && rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm \
    && rpm -Uvh https://mirror.webtatic.com/yum/el6/latest.rpm \
    && rpm -Uvh http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm \
    && yum install -y php70w php70w-cli php70w-common php70w-embedded php70w-interbase php70w-process php70w-xml


WORKDIR /usr/local/share/php-composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('SHA384', 'composer-setup.php') === '55d6ead61b29c7bdee5cccfb50076874187bd9f21f65d8991d46ec5cc90518f447387fb9f76ebae1fbbacf329e583e30') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php \
    && php -r "unlink('composer-setup.php');" \
    && /usr/local/share/php-composer/composer.phar require influxdb/influxdb-php

WORKDIR /

ENTRYPOINT export INFLUXDB_META_DIR=/influxdb/meta \
    && export INFLUXDB_DATA_DIR=/influxdb/data \
    && export INFLUXDB_DATA_WAL_DIR=/influxdb/wal \
    && chown -R influxdb:influxdb /influxdb \
    && /etc/init.d/influxdb restart \
    && service httpd start \
    && echo "visit localhost:80 to see the demo" \
    && /bin/bash
