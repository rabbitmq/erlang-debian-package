# Erlang/OTP packages for Debian and Ubuntu

This Git repository contains files to create packages of Erlang/OTP
for Debian and Ubuntu. This work is based on the [official Debian
package](https://salsa.debian.org/erlang-team/packages/erlang). The
RabbitMQ team adapted the upstream package to provide packages for many
versions of Erlang/OTP and many releases of both Debian and Ubuntu.

## Supported versions of Erlang/OTP and Debian/Ubuntu

Packages are published to Debian repositories on Bintray. We support the
following distributions:

*   Debian Jessie
*   Debian Stretch
*   Ubuntu 16.04 (Xenial)
*   Ubuntu 18.04 (Bionic)

For each distribution, we provide the following versions of Erlang/OTP:

*   Erlang R16B03-1
*   Erlang 19.x (whatever is the latest patch release)
*   Erlang 20.x (*ditto*)
*   Erlang 21.x (*ditto*)
*   Erlang built from [their Git `master` branch](https://github.com/erlang/otp),
    i.e. what will become Erlang 22.0 in the future.

## How to configure the Debian repository?

1.  You need to know the Debian or Ubuntu distribution you are using to
    select the appropriate repository.

    This determines the dsitribution codename to use in the
    `sources.list` below. The codename is the Debian or Ubuntu
    distribution codename in lowercase. For example:

    *   `jessie`
    *   `stretch`
    *   `xenial`
    *   `bionic`

2.  You need to decide if you want to track a specific release branch
    of Erlang/OTP (e.g. Erlang 20.x) or install whatever is the latest
    and greatest.

    This determines which Debian repository component you need to set in
    the `sources.list` during the next step:

    *   If you want to track whatever is the very latest version of
        Erlang/OTP, the component should be `erlang`.

    *   If you want to track a specific major version, the component
        should be e.g. `erlang-20.x`.

    Note that:

    *   To track Erlang R16B03-1, the component is `erlang-16.x` to be
        consistent with other components.

    *   The package built from the `master` branch is available through
        the `erlang-22.x` component only: those development versions are
        not published to the `erlang` component.

2.  Configure the `sources.list`:

    ```
    # In e.g. /etc/apt/sources.list.d/rabbitmq-erlang.list
    deb http://dl.bintray.com/rabbitmq/debian $distribution $component
    ```

    Be sure to replace `$distribution` and `$component` by whatever you
    chose in the previous steps!

3.  Add the PGP key used to sign the repository:

    ```sh
    wget -O - 'http://www.rabbitmq.com/rabbitmq-release-signing-key.asc' \
      | sudo apt-key add -
    ```

4.  You may want to configure an `apt_preferences(5)`.

    Meta-packages such as `erlang-nox` do not pin the version of their
    dependencies. It means that out-of-the-box, if for instance you
    install `erlang-nox` 1:16.b.3.1-1 on Debian Stretch, it may pull
    `erlang-base` 1:19.2.1+dfsg-2+deb9u1 from the official Debian
    repository.

    To prevent that, you can instruct apt-get(8) to always take
    Erlang/OTP packages from the RabbitMQ's Bintray repository:

    ```
    Package: erlang*
    Pin: release o=Bintray
    Pin-Priority: 1000
    ```

    Please read the [`apt_preferences(5)` man
    page](https://manpages.debian.org/stretch/apt/apt_preferences.5.en.html)
    for the meaning of `Pin-Priority` values.

5.  Update the repository indexes copy:

    ```sh
    sudo apt-get update
    ```

## What are the differences with other Debian packages providers?

### Differences with the official Debian packages

#### Provided Erlang/OTP versions

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

Erlang solutions provides packages for many Erlang/OTP minor
releases. However, they publish packages for the first release
on a minor branch only, not the subsequent patch releases. In
other words, they follow the Erlang/OTP sources published on
[erlang.org](http://www.erlang.org/downloads). Instead, we want to
provide the latest patches, only made available as Git tags.

#### Supported architectures

Erlang Solutions publishes packages for `i386` (32-bit), something we do
not do.

#### Distributions coverage

We also try to be consistent accross the Debian/Ubuntu distributions we
support: we want all packages to be available for all of them.

Erlang Solutions supports more distributions, but older ones may not
receive the latest Erlang/OTP release branches.

#### All-in-one package

In addition to the fine-grained package set also provided by Debian
and Ubuntu, Erlang Solutions publishes a convenient all-in-one package
called `esl-erlang`.

We do not do that and have no plan to provide one.

## How packages are built?

We use a [public CI
pipeline](https://ci.rabbitmq.com/teams/main/pipelines/erlang-debian-package)
to automate the compilation for all versions of Erlang/OTP and all
Debian/Ubuntu distributions.

When a new patch release is tagged by the Erlang/OTP team, it is
automatically picked up by the pipeline to update the Debian changelog
and build and publish a new package.

Packages are tested by this CI pipeline once they are published to
Bintray to also test the repository itself.
