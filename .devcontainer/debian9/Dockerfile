FROM debian:stretch

RUN useradd -u 11111 -s /bin/bash -m dev

# If the primary group of the user running this container is 100 (users) on
# the host system, GID remapping during Dev Container setup may fail because
# group 100 already exists in the container.
RUN groupdel -f users

CMD ["sleep", "infinity"]
