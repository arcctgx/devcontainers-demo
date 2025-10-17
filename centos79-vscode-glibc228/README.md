# centos79-vscode-glibc228

Since VSCode 1.99 the remote server component requires `glibc-2.28` or above.
This makes it impossible to use VSCode Dev Containers extension with container
images based on CentOS 7, or other RHEL7 derivatives built with `glibc-2.17`.

[VSCode remote server documentation][1] describes a way to run the remote server
on an older Linux distribution, provided that `glibc-2.28` and `patchelf` are
available.

This `Dockerfile` builds an image that provides `glibc-2.28` and `patchelf-0.18.0`
built from source in a CentOS 7.9 container and installed into `/opt/`. This
image is meant as a medium for distributing pre-built binaries to other images.

`glibc` binaries are not stripped. Stripping would reduce the image size from
about 160 MB to about 60 MB, but it's risky and could cause breakage. See the
comment in the `Dockerfile` for details.

## Usage

Build the image:

```sh
docker build -t centos79-vscode-glibc228 .
```

Use the pre-built `glibc` and `patchelf` binaries in another container image:

```Dockerfile
FROM centos:7.9.2009

COPY --from=centos79-vscode-glibc228 /opt/ /opt/
```

[1]: https://code.visualstudio.com/docs/remote/faq#_can-i-run-vs-code-server-on-older-linux-distributions
