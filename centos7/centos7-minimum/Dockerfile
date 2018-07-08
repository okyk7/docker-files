FROM centos:7

RUN rpm -import http://rpms.remirepo.net/RPM-GPG-KEY-remi && \
    rpm -import http://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-7 && \
    yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm && \
    yum install -y http://rpms.remirepo.net/enterprise/remi-release-7.rpm && \
    yum install -y yum-utils && \
    yum update -y

RUN yum install -y httpd-devel.x86_64 wget git unzip vim rsync htop finger jq zip

# タイムゾーン系
RUN localedef -f UTF-8 -i ja_JP ja_JP.UTF-8
RUN unlink /etc/localtime && ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime
ENV LANG="ja_JP.UTF-8" \
    LANGUAGE="ja_JP:ja" \
    LC_ALL="ja_JP.UTF-8"

# apache設定
RUN rm /etc/httpd/conf.d/welcome.conf \
    && sed -i -e "s/Options\ Indexes\ FollowSymLinks/Options\ -Indexes\ +FollowSymLinks/g" /etc/httpd/conf/httpd.conf \
    && sed -i "s/\/var\/www\/html/\/var\/www/g" /etc/httpd/conf/httpd.conf \
    && echo "FileETag None" >> /etc/httpd/conf/httpd.conf \
    && echo "ServerTokens Prod" >> /etc/httpd/conf/httpd.conf \
    && echo "ServerSignature Off" >> /etc/httpd/conf/httpd.conf

# ユーザー追加
RUN useradd develop
RUN echo 'develop' | passwd develop --stdin

WORKDIR /etc
COPY vimrc .
RUN echo "alias vi='vim'" >> /etc/bashrc
RUN echo "alias view='vim -R'" >> /etc/bashrc
WORKDIR /root

# privileged用途
# CMDはコンテナ起動時実行、RUNはイメージビルド時実行
CMD /sbin/init