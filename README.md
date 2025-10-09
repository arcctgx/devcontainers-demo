# Developer Containers demo

The `.devcontainer` directory contains definitions of various developer
environments based on different container images. The environments are
roughly divided into two Linux distribution families:

* Debian:
  * Debian 9 (Stretch)
  * Ubuntu 20.04 (Focal Fossa)
* Fedora / RHEL:
  * Fedora 28
  * CentOS 7.9
  * Rocky Linux 8.10

Each subdirectory provides an environment definition in JSON format, and a
`Dockerfile` for customizing the original container image. These adjustments
are required for the Dev Containers VSCode extension to work correctly.

It is also possible to directly use a container image without any changes,
or to use a `docker-compose.yml` file for complex environment setup. See
<https://containers.dev> for the complete documentation.

## Notes about Dockerfile customizations

The Dev Containers extension requires a non-root user so that it can map
the UID and GID from the host system into the container to avoid permission
issues. The non-root user doesn't have to be explicitly activated by the
`USER` directive in the `Dockerfile`, but the account must exist.

The `Dockerfile` of Fedora 28 environment explicitly installs `libstdc++`
package. It is required by Dev Containers extension. It is not installed in
CentOS 7.9 or Rocky Linux 8.10 environments, because it is already present
in their respective container images.

In all evironments the group `users` is removed before creating the non-root
user account. This is done to accomodate host systems where User Private Groups
are not used, where the existence of group 100 (`users`) results in a failure
of Dev Containers setup script. This is not needed on UPG-based systems, but
should not cause problems there.

The most interesting aspect is a workaround required for Fedora / RHEL family
environments. When the non-root user account is created, its home directory
is created inside `/run`, and the directory creation is manual, not delegated
to `adduser`. This workaround prevents problems where non-root user is unable
to write to its home directory after UID/GID remapping during Dev Containers
setup phase, despite the permissions and modes being set correctly. It is not
clear to me why that happens at all, why only in Fedora derivatives, or why
creating a directory in `/run` (or `/tmp`) is a workaround. It may be related
to `overlayfs` driver bug, so it could be specific to the Linux kernel version
I have on my machine.
