# Erlang/OTP packages for Debian and Ubuntu

This repository contains release automation files for Debian and Ubuntu packages of Erlang/OTP. It is
maintained by the RabbitMQ team.

This work is based on the [official Erlang/OTP package for Debian](https://salsa.debian.org/erlang-team/packages/erlang).
The RabbitMQ team adapted the upstream package to produce packages for more/different
combinations of Erlang/OTP, Debian and Ubuntu releases.

Unlike Team RabbitMQ's [zero dependency Erlang/OTP RPM](https://github.com/rabbitmq/erlang-rpm), these packages
are not monolithic and use the same dependency tree as the official Debian packages of Erlang.

## Supported Erlang/OTP and Debian/Ubuntu Combinations

Packages are published to a [Launchpad PPA](https://launchpad.net/~rabbitmq/+archive/ubuntu/rabbitmq-erlang)
and [Cloudsmith.io](https://cloudsmith.io/~rabbitmq/repos/rabbitmq-erlang/packages/?sort=-version&q=filename%3Adeb%24).

The following distributions are currently [supported](https://www.rabbitmq.com/install-debian.html#apt-launchpad-erlang):

 * Ubuntu 22.04 (Jammy)
 * Ubuntu 20.04 (Focal)
 * Ubuntu 18.04 (Bionic)
 * Debian Bullseye
 * Debian Buster

For each distribution, the following release series of Erlang/OTP can be produced:

 * `26.x`
 * `25.3.x`
 
For every release series, only the latest minor series is supported.
 

## Apt Repository Setup

### Launchpad PPA

See the section on [provisioning modern Erlang versions from Launchpad](https://www.rabbitmq.com/install-debian.html#apt-launchpad-erlang) in the RabbitMQ Ubuntu and Debian installation guide.

### Community Mirror of Cloudsmith

RabbitMQ Cloudsmith.io repository has a monthly traffic quota.
As such, we recommend that a community mirror of that repository
is used instead.

``` shell
#!/usr/bin/sh

sudo apt-get install curl gnupg apt-transport-https -y

## Team RabbitMQ's main signing key
curl -1sLf "https://keys.openpgp.org/vks/v1/by-fingerprint/0A9AF2115F4687BD29803A206B73A36E6026DFCA" | sudo gpg --dearmor | sudo tee /usr/share/keyrings/com.rabbitmq.team.gpg > /dev/null
## Community mirror of Cloudsmith: modern Erlang repository
curl -1sLf https://ppa1.novemberain.com/gpg.E495BB49CC4BBE5B.key | sudo gpg --dearmor | sudo tee /usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg > /dev/null

## Add apt repositories maintained by Team RabbitMQ
sudo tee /etc/apt/sources.list.d/rabbitmq.list <<EOF
## Provides modern Erlang/OTP releases
##
deb [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa1.novemberain.com/rabbitmq/rabbitmq-erlang/deb/ubuntu jammy main
deb-src [signed-by=/usr/share/keyrings/rabbitmq.E495BB49CC4BBE5B.gpg] https://ppa1.novemberain.com/rabbitmq/rabbitmq-erlang/deb/ubuntu jammy main
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

## Differences from Other Debian Package Providers

### Differences from the Official Debian Packages

#### Provided Erlang/OTP Versions

The main difference is the provided versions:

*   The official Debian repository provides a single Erlang/OTP minor
    release for a given Debian distribution and this is frozen when the
    distribution is released. The package only receives bug fixes.

*   Our repository provides many Erlang/OTP major releases for many
    distributions, taking the latest minor+patch release. It also
    provides a preview of the next in-development release branch of
    Erlang/OTP.

#### Supported architectures

Another difference is the supported architectures: we only support
`amd64` whereas Debian and Ubuntu provides packages for many more
architectures.

### Differences with the Erlang Solutions packages

#### Provided Erlang/OTP versions

Erlang Solutions [provides Debian packages](https://packages.erlang-solutions.com/erlang/) for many Erlang/OTP minor
releases. However, patch releases in each series are often delayed or never published.
In other words, they follow the Erlang/OTP sources published on
[erlang.org](http://www.erlang.org/downloads). This repository strives to
make the latest patch releases of Erlang/OTP easy to consume.

#### Supported Architectures

Erlang Solutions produces 32-bit (`i386`) packages. This repository
only provides 64-bit packages.

#### Distributions Coverage

One of the primary goals of this package is to make latest Erlang release series
available to most popular Debian/Ubuntu distributions.

Erlang Solutions supports more distributions but older ones
typically don't have the latest Erlang/OTP releases or entire series.

#### All-in-one Package

In addition to the fine-grained package set also provided by Debian
and Ubuntu, Erlang Solutions publishes a convenient all-in-one package
called `esl-erlang`.

This repository doesn't provide such packages.

## How the Packages are Produced

Team RabbitMQ maintains a [public Concourse pipeline](https://ci.rabbitmq.com/teams/main/pipelines/erlang-debian-package)
that automates the build process for all versions of Erlang/OTP and all
Debian/Ubuntu distributions.

When a new patch release is tagged in the Erlang/OTP repository, it is
automatically picked up by the pipeline. Then the package changelog
is updated and a new package is built, tested and published to Bintray.

The package are tested by a different part of the same Concourse pipeline
once they are published to Bintray. This makes sure the final repository
is indirectly tested.


## Copyright and License

(c) 2018-2021 VMware, Inc and its affiliates.

Released under the [Apache Software License 2.0](https://github.com/rabbitmq/erlang-rpm-packaging/blob/master/Erlang_ASL2_LICENSE.txt),
same as Erlang/OTP starting with 18.0.
