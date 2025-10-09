FROM fedora:28

RUN yum install -y libstdc++-8.3.1-2.fc28 && yum clean all

RUN groupdel -f users && mkdir /run/home-dev && useradd -d /run/home-dev -u 11111 -s /bin/bash -m dev
