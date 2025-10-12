FROM centos:7.9.2009

RUN useradd -u 11111 -s /bin/bash -m dev && chmod o+x /home/dev

# If the primary group of the user running this container is 100 (users) on
# the host system, GID remapping during Dev Container setup may fail because
# group 100 already exists in the container.
RUN groupdel -f users

CMD ["sleep", "infinity"]
