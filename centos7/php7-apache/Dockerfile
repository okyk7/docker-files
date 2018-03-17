FROM centos:7

RUN rpm -import http://rpms.remirepo.net/RPM-GPG-KEY-remi && \
    rpm -import http://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-7 && \
    yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm && \
    yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm && \
    yum install -y yum-utils && \
    yum-config-manager --enable remi-php71 && \
    yum update -y && \
    yum install -y \
    php71-php.x86_64 \
    php71-php-bcmath.x86_64 \
    php71-php-cli.x86_64 \
    php71-php-common.x86_64 \
    php71-php-devel.x86_64 \
    php71-php-gd.x86_64 \
    php71-php-intl.x86_64 \
    php71-php-json.x86_64 \
    php71-php-ldap.x86_64 \
    php71-php-mbstring.x86_64 \
    php71-php-mcrypt.x86_64 \
    php71-php-mysqlnd.x86_64 \
    php71-php-pdo.x86_64 \
    php71-php-pear.noarch \
    php71-php-xml.x86_64 \
    php71-php-ast.x86_64 \
    php71-php-opcache.x86_64 \
    php71-php-pecl-zip.x86_64 \
    php71-php-pecl-memcached.x86_64 \
    php71-php-pdo.x86_64 \
    php71-php-pgsql.x86_64 \
    php71-php-pear.noarch \
    php71-php-pecl-xdebug.x86_64 && \
    ln -s /usr/bin/php71 /usr/bin/php && \
    ln -s /etc/opt/remi/php71/php.ini /etc/php.ini && \
    ln -s /etc/opt/remi/php71/php.d /etc/php.d && \
    ln -s /etc/opt/remi/php71/pear.conf /etc/pear.conf && \
    ln -s /etc/opt/remi/php71/pear /etc/pear

RUN yum install -y httpd-devel.x86_64 wget git unzip vim rsync htop finger jq zip

RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer

# タイムゾーン系
RUN localedef -f UTF-8 -i ja_JP ja_JP.UTF-8
RUN unlink /etc/localtime && ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
RUN rm -f /etc/rpm/macros.image-language-conf && \
    sed -i '/^override_install_langs=/d' /etc/yum.conf && \
    yum -y reinstall glibc-common && \
    yum clean all
ENV LANG="ja_JP.UTF-8" \
    LANGUAGE="ja_JP:ja" \
    LC_ALL="ja_JP.UTF-8"

# apache設定
RUN rm /etc/httpd/conf.d/welcome.conf \
    && sed -i -e "s/Options\ Indexes\ FollowSymLinks/Options\ -Indexes\ +FollowSymLinks/g" /etc/httpd/conf/httpd.conf \
    && sed -i "s/\/var\/www\/html/\/var\/www/g" /etc/httpd/conf/httpd.conf \
    && echo "FileETag None" >> /etc/httpd/conf/httpd.conf \
    && sed -i -e "s/expose_php\ =\ On/expose_php\ =\ Off/g" /etc/php.ini \
    && sed -i -e "s|;date.timezone =|date.timezone = Asia/Tokyo|" /etc/php.ini \
    && sed -i -e "s/\;error_log\ =\ php_errors\.log/error_log\ =\ \/var\/log\/php_errors\.log/g" /etc/php.ini \
    && echo "ServerTokens Prod" >> /etc/httpd/conf/httpd.conf \
    && echo "ServerSignature Off" >> /etc/httpd/conf/httpd.conf

# ユーザー追加
RUN useradd develop
RUN echo 'develop' | passwd develop --stdin

WORKDIR /etc
COPY vimrc .
RUN echo "alias vi='vim'" >> /etc/bashrc

# privileged用途
# CMDはコンテナ起動時実行、RUNはイメージビルド時実行
CMD /sbin/init