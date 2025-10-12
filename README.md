# Development Containers demo

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
are required for the Dev Containers VSCode extension to work correctly. The
environment itself is started by `docker-compose`, based on the provided
`docker-compose.yml` file.

It is also possible to directly use a container image without any changes,
or to only use a `Dockerfile` for environment setup. See the documentation
at <https://containers.dev/implementors/spec/#orchestration-options> for
in-depth description.

## Notes about Dockerfile customizations

The Dev Containers extension requires a non-root user so that it can map
the UID and GID from the host system into the container to avoid permission
issues. The non-root user doesn't have to be explicitly activated by the
`USER` directive in the `Dockerfile`, but the account must exist.

The `Dockerfile` of Fedora 28 environment explicitly installs `libstdc++`
package. It is required by Dev Containers extension. It is not installed in
CentOS 7.9 or Rocky Linux 8.10 environments, because it is already present
in their respective container images.

In all evironments the group `users` is removed after creating the non-root
user account. This is done to accomodate host systems where User Private Groups
are not used, where the existence of group 100 (`users`) results in a failure
of Dev Containers setup script. This is not needed on UPG-based systems, but
should not cause problems there.

The most interesting aspect is a workaround required for Fedora / RHEL family
environments. After creating the non-root user account, its home directory
requires an exec bit for the world to be set (by default the world has no
permissions set at all). Missing world exec bit results in the non-root user
being unable to write to their own home directory after UID/GID remapping
done during Dev Containers setup phase, despite user and group permissions and
modes being set correctly. It is not clear to me why that happens at all, and
why only on Fedora derivatives, not Debian. It may be related to `overlayfs`
driver bug, so it could be specific to the Linux kernel version on my machine.
