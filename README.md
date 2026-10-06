# Lavender JRE

A Docker container providing OpenJDK 17, 21, and 25 for arm/v7 and other architectures.

``ghcr.io/retrodaredevil/lavender-jre``

```shell
docker pull ghcr.io/retrodaredevil/lavender-jre:21-ubuntu-noble
docker run --rm ghcr.io/retrodaredevil/lavender-jre:21-ubuntu-noble java --version
```

## Features

### Updated Regularly

The images pushed to GitHub Container Registry are updated every Tuesday at 10:37 UTC (05:37 EST or 06:37 EDT).
Please keep this in mind for any dependency you put upon this project in your CI/CD.
Additionally, builds may be updated at any time when I decide to push changes to this repository.

### Many Supported Architectures

The workflow builds each variant for the platforms listed below.
Ubuntu Noble variants target 6 architectures, Debian Trixie variants target 8, and Debian Bookworm variants target 5.
Platform availability can change when the base images are updated.

### As close to the base image as possible

Don't be scared! Take a look at the [Dockerfile](./Dockerfile).
This is about as simple as you can get.
Since this has a single layer on top of the base image, you can use it as if it was the base image with Java installed on it.

You can also extend this image by using it as a base image in your own Dockerfile.
Since this image already has Java installed, your build times should shrink considerably because of how much space Java can take up.


## Variants

The [workflow](./.github/workflows/docker-push.yml) builds the following tags under `ghcr.io/retrodaredevil/lavender-jre`:

| Base image | Java versions | Tags |
| --- | --- | --- |
| Ubuntu 24.04 (`ubuntu:noble`) | 17, 21, 25 | `17-ubuntu-noble`, `21-ubuntu-noble`, `25-ubuntu-noble` |
| Debian 13 (`debian:trixie`) | 21, 25 | `21-debian-trixie`, `25-debian-trixie` |
| Debian 13 slim (`debian:trixie-slim`) | 21, 25 | `21-debian-trixie-slim`, `25-debian-trixie-slim` |
| Debian 12 (`debian:bookworm`) | 17 | `17-debian-bookworm` |
| Debian 12 slim (`debian:bookworm-slim`) | 17 | `17-debian-bookworm-slim` |

Ubuntu Jammy and Debian Bullseye tags are no longer rebuilt by this workflow.

### Supported Platforms

Regular and slim Debian variants use the same platform lists.

| Base image family | Platforms |
| --- | --- |
| Ubuntu Noble | `linux/amd64`, `linux/arm/v7`, `linux/arm64/v8`, `linux/ppc64le`, `linux/riscv64`, `linux/s390x` |
| Debian Trixie | `linux/amd64`, `linux/arm/v5`, `linux/arm/v7`, `linux/arm64/v8`, `linux/386`, `linux/ppc64le`, `linux/riscv64`, `linux/s390x` |
| Debian Bookworm | `linux/amd64`, `linux/arm/v7`, `linux/arm64/v8`, `linux/386`, `linux/ppc64le` |

## Exceptions

The current [official Debian image manifests](https://github.com/docker-library/official-images/blob/master/library/debian) omit `linux/arm/v5` and `linux/s390x` for Bookworm, so these platforms are excluded from its builds.
`linux/mips64le` is also unsupported and is absent from the current base image manifests.

## Why Make This?

There are countless other Docker images containing JREs, but many of them don't have arm/v7 support, 
which means not supporting Raspberry Pi 2 v1.2s or Raspberry Pi 3s.

`lavender-jre` will always be a simple Debian or Debian derivative based Docker image with the corresponding OpenJDK package installed.

## Building Yourself

Builds are automated, but you can also build these images locally.
Both `BASE_IMAGE` and `PACKAGE_NAME` must be supplied as build arguments.
For builds targeting other CPU architectures, configure QEMU emulation or use native builders for those platforms.

Docker documentation:

* https://docs.docker.com/build/building/multi-platform/
* https://docs.docker.com/get-started/docker-concepts/building-images/build-tag-and-publish-an-image/#tagging-images
* https://docs.docker.com/build/building/variables/

```shell
platforms="linux/amd64,linux/arm/v7"
docker buildx create --use
docker buildx build \
  --build-arg BASE_IMAGE=ubuntu:noble \
  --build-arg PACKAGE_NAME=openjdk-21-jre \
  --platform "$platforms" \
  --tag lavender-jre:test-local-latest \
  --output type=oci,dest=lavender-jre.tar \
  .
```

This writes a multi-platform OCI archive to `lavender-jre.tar`.
