# Erlang/OTP packages for Debian and Ubuntu

This repository contains release automation files for Debian and Ubuntu packages of Erlang/OTP. It is
maintained by the RabbitMQ team.

This work is based on the [official Erlang/OTP package for Debian](https://salsa.debian.org/erlang-team/packages/erlang).
The RabbitMQ team adapted the upstream package to produce packages for more/different
combinations of Erlang/OTP, Debian and Ubuntu releases.

Unlike Team RabbitMQ's [zero dependency Erlang/OTP RPM](https://github.com/rabbitmq/erlang-rpm), these packages
are not monolithic and use the same dependency tree as the official Debian packages of Erlang.

## Supported Erlang/OTP and Debian/Ubuntu Combinations

Packages are published to a [Debian repository on Bintray](https://bintray.com/rabbitmq-erlang/debian). The following
distributions are currently supported:

 * Ubuntu 18.04 (Bionic)
 * Ubuntu 16.04 (Xenial)
 * Debian Buster
 * Debian Stretch

For each distribution, the following release series of Erlang/OTP are packaged:

 * `22.x` (the latest patch release)
 * `21.x` (ditto)
 * `20.x` (ditto)
 * [Erlang master](https://github.com/erlang/otp)

## Apt Repository Setup

### Enable apt HTTPS Transport

In order for apt to be able to download Erlang packages from Bintray,
the `apt-transport-https` package must be installed:

```sh
sudo apt-get install apt-transport-https
```

### Signing Key

For apt to be able to verify package signatures and trust them, add
the [signing key used to sign RabbitMQ releases](https://www.rabbitmq.com/signatures.html) to `apt-key`:

```sh
wget -O - 'https://dl.bintray.com/rabbitmq/Keys/rabbitmq-release-signing-key.asc' | sudo apt-key add -
```

### Source List File

As with all 3rd party Apt (Debian) repositories, a file describing the repository
must be placed under the `/etc/apt/sources.list.d/` directory.
`/etc/apt/sources.list.d/bintray.rabbitmq.list` is the recommended location.

The file should have a source (repository) definition line that uses the following
pattern:

```
# See below for supported distribution and component values
deb https://dl.bintray.com/rabbitmq-erlang/debian $distribution $component
```

The next couple of sections discusses what distribution and component values
are supported.

### Distribution

In order to set up an apt repository that provides the correct package, a few
decisions have to be made. One is determining the distribution name. It comes
from the Debian or Ubuntu release used:

 * `bionic` for Ubuntu 18.04
 * `xenial` for Ubuntu 16.04
 * `buster` for Debian Buster
 * `stretch` for Debian Stretch

### Erlang/OTP Version

Another is what Erlang/OTP release version should be provisioned. It is possible to track
a specific series (e.g. `20.x`) or install the most recent version available. The choice
determines what Debian repository `component` will be configured.

It is possible to pin the package to a specific version. This will be covered below.

Consider the following repository file at `/etc/apt/sources.list.d/bintray.rabbitmq.list`:

```
deb https://dl.bintray.com/rabbitmq-erlang/debian bionic erlang
```

It configures apt to install the most recent Erlang/OTP version available in the
repository and use packages for Ubuntu 18.04 (Bionic).

For Debian Stretch the file would look like this:

```
deb https://dl.bintray.com/rabbitmq-erlang/debian stretch erlang
```

To use the most recent `21.x` patch release available, switch the component
to `erlang-21.x`:

```
deb https://dl.bintray.com/rabbitmq-erlang/debian bionic erlang-21.x
```

`erlang-20.x`, `erlang-19.x`, and `erlang-16.x` are the components for Erlang 20.x,
19.x and R16B03, respectively.


### Installing Packages

After updating the list of `apt` sources it is necessary to run `apt-get update`:

```sh
sudo apt-get update -y
```

Then packages can be installed just like with the standard Debian repositories:

```sh
# This is recommended. Metapackages such as erlang and erlang-nox must only be used
# with apt version pinning. They do not pin their dependency versions.
sudo apt-get install -y erlang-base \
                        erlang-asn1 erlang-crypto erlang-eldap erlang-ftp erlang-inets \
                        erlang-mnesia erlang-os-mon erlang-parsetools erlang-public-key \
                        erlang-runtime-tools erlang-snmp erlang-ssl \
                        erlang-syntax-tools erlang-tftp erlang-tools erlang-xmerl
```


### Package/Repository Pinning (Apt Preferences)

Since Erlang/OTP packages are available for any Debian and Ubuntu distribution,
once this repository is added via a source file there will be two or even more
repositories that provide packages under the same name.

To add insult to injury, meta-packages such as `erlang-nox` do not pin the version of their
dependencies. It means that out-of-the-box, if for instance you
install `erlang-nox` 1:20.3.8.21-1 on Debian Stretch, it may pull
`erlang-base` 1:19.2.1+dfsg-2+deb9u1 from the official Debian
repository.

[`apt_preferences(5)`](https://manpages.debian.org/stretch/apt/apt_preferences.5.en.html)
can be used to instruct `apt` to prefer packages from this repository.

Place a file with following contents to `/etc/apt/preferences.d/erlang`:

```
Package: erlang*
Pin: release o=Bintray
Pin-Priority: 1000
```

After updating `apt` preferences it is necessary to run `apt-get update`:

```sh
sudo apt-get update -y
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

(c) 2018-current Pivotal Software, Inc.

Released under the [Apache Software License 2.0](https://github.com/rabbitmq/erlang-rpm-packaging/blob/master/Erlang_ASL2_LICENSE.txt),
same as Erlang/OTP starting with 18.0.
