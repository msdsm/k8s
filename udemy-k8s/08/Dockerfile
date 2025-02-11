FROM amd64/centos:7

COPY . /tmp/debug

RUN  \
# Configure yum repositories
sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-* && \
sed -i 's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' /etc/yum.repos.d/CentOS-* && \
# Install MongoDB shell, tools.
mv /tmp/debug/mongodb-org-4.0.repo /etc/yum.repos.d && \
yum install -y mongodb-org-shell-4.0.5 mongodb-org-tools-4.0.5 && \
# Install ip , ifconfig command.
yum install -y iproute net-tools &&\
# Install jq
curl -o /usr/local/bin/jq -L https://github.com/stedolan/jq/releases/download/jq-1.6/jq-linux64 && \
chmod +x /usr/local/bin/jq && \
# Delete cache files.
yum clean all