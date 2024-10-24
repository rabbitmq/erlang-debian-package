# Erlang/OTP packages for Debian and Ubuntu

This repository contains release automation files for Debian and Ubuntu packages of Erlang/OTP. It is
maintained by the RabbitMQ team.

This work is based on the [official Erlang/OTP package for Debian](https://salsa.debian.org/erlang-team/packages/erlang).
The RabbitMQ team adapted the upstream package to produce packages for more/different
combinations of Erlang/OTP, Debian and Ubuntu releases.

Unlike Team RabbitMQ's [zero dependency Erlang/OTP RPM](https://github.com/rabbitmq/erlang-rpm), these packages
are not monolithic and use the same dependency tree as the official Debian packages of Erlang.

## Supported Erlang/OTP and Debian/Ubuntu Combinations

Packages are published to several Launchpad PPAs:

 * [`~rabbitmq/rabbitmq-erlang-26`](https://launchpad.net/~rabbitmq/+archive/ubuntu/rabbitmq-erlang-26) (26.x)
 * [`~rabbitmq/rabbitmq-erlang-25`](https://launchpad.net/~rabbitmq/+archive/ubuntu/rabbitmq-erlang-25) (25.3.x)

as well a Cloudsmith.io mirror (see below).

The following distributions are currently covered by at least one (e.g. Erlang 26.x)
release series of this package, corresponding to the [RabbitMQ Erlang requirement matrix](https://www.rabbitmq.com/docs/which-erlang):

 * Ubuntu 24.04 (Noble)
 * Ubuntu 22.04 (Jammy)
 * Debian Trixie
 * Debian Bookworm
 * Debian Bullseye

For each distribution, the following release series of Erlang/OTP can be produced:

 * `26.x`
 * `25.3.x`
 
For every release series, only the latest minor series is supported.
 

## Apt Repository Setup

### Launchpad PPA

This package is build and published to [Ubuntu Launchpad](https://launchpad.net/~rabbitmq):

 * [`~rabbitmq/rabbitmq-erlang-26`](https://launchpad.net/~rabbitmq/+archive/ubuntu/rabbitmq-erlang-26) (26.x)
 * [`~rabbitmq/rabbitmq-erlang-25`](https://launchpad.net/~rabbitmq/+archive/ubuntu/rabbitmq-erlang-25) (25.3.x)

Launchpad provides both `amd64` and `arm64` builds but only the latest published version
(older releases are not available).

### Community Mirror of Cloudsmith

RabbitMQ Cloudsmith.io repository has a monthly traffic quota.
As such, we recommend that a community mirror of that repository
is used instead.

``` shell
#!/bin/sh

sudo apt-get install curl gnupg apt-transport-https -y

## Team RabbitMQ's main signing key
curl -1sLf "https://keys.openpgp.org/vks/v1/by-fingerprint/0A9AF2115F4687BD29803A206B73A36E6026DFCA" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/com.rabbitmq.team.gpg > /dev/null
## Community mirror of Cloudsmith: modern Erlang repository
curl -1sLf https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-erlang.E495BB49CC4BBE5B.key | sudo gpg --dearmor | sudo tee /usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg > /dev/null
## Community mirror of Cloudsmith: RabbitMQ repository
curl -1sLf https://github.com/rabbitmq/signing-keys/releases/download/3.0/cloudsmith.rabbitmq-server.9F4587F226208342.key | sudo gpg --dearmor | sudo tee /usr/share/keyrings/rabbitmq.9F4587F226208342.gpg > /dev/null

## Add apt repositories maintained by Team RabbitMQ
sudo tee /etc/apt/sources.list.d/rabbitmq.list <<EOF
## Provides modern Erlang/OTP releases
##
deb [arch=amd64 signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa1.rabbitmq.com/rabbitmq/rabbitmq-erlang/deb/ubuntu noble main
deb-src [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa1.rabbitmq.com/rabbitmq/rabbitmq-erlang/deb/ubuntu noble main

# another mirror for redundancy
deb [arch=amd64 signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa2.rabbitmq.com/rabbitmq/rabbitmq-erlang/deb/ubuntu noble main
deb-src [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa2.rabbitmq.com/rabbitmq/rabbitmq-erlang/deb/ubuntu noble main
EOF

## Update package indices
sudo apt-get update -y

## Install Erlang packages
sudo apt-get install -y erlang-base \
                        erlang-asn1 erlang-crypto erlang-eldap erlang-ftp erlang-inets \
                        erlang-mnesia erlang-os-mon erlang-parsetools erlang-public-key \
                        erlang-runtime-tools erlang-snmp erlang-ssl \
                        erlang-syntax-tools erlang-tftp erlang-tools erlang-xmerl
```


## Differences from the Official Debian Packages

### Provided Erlang/OTP Versions

The main difference is the provided versions:

*   The official Debian repository provides a single Erlang/OTP minor
    release for a given Debian distribution and this is frozen when the
    distribution is released. The package only receives bug fixes.

*   Our repository provides many Erlang/OTP major releases for many
    distributions, taking the latest minor+patch release. It also
    provides a preview of the next in-development release branch of
    Erlang/OTP.

### Supported architectures

Another difference is the supported architectures: this repository
produces `amd64` packages plus Launchpad builds an `arm64` version
whereas Debian and Ubuntu provides packages for many more architectures.


## How the Packages are Produced

Packages are produced using a GitHub Actions [workflow that is publicly available](https://github.com/rabbitmq/erlang-packages/).

The packages produced from this source repository are then 
published to external packaging services such as [Launchpad](https://launchpad.net/~rabbitmq) and Cloudsmith,
and then to [Cloudsmith mirrors](https://rabbitmq.com/install-debian.html#apt-cloudsmith).

When a new patch release is tagged in the Erlang/OTP repository, it is
automatically picked up by the pipeline. Then the package changelog
is updated and a new package is built, tested and published to Bintray.


## Copyright and License

(c) 2023-2024 Broadcom. "Broadcom" may refer to Broadcom, Inc or its affiliates.

(c) 2018-2023 VMware, Inc and its affiliates.

Released under the [Apache Software License 2.0](https://github.com/rabbitmq/erlang-rpm-packaging/blob/master/Erlang_ASL2_LICENSE.txt),
same as Erlang/OTP starting with 18.0.
