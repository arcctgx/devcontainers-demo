FROM fedora:28

RUN yum install -y libstdc++-8.3.1-2.fc28 && yum clean all

RUN groupdel -f users && useradd -u 11111 -s /bin/bash -m dev && chmod o+x /home/dev
