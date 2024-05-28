---
title: Learning how to contribute to Debian through packaging
date: 2024-05-23 12:00:00 -0300
categories: [Experiences with FLOSS, starting]
tags: [FLOSS, packaging, debian, MAC0470]
---

Until now my journey into FLOSS projects were just about contributing with code, but there is another ways to do it and one great of them is by creating and maintaining packages. In this post I relate my experience following some tutorials made by [Joenio Marques](https://joenio.me/), a Debian contributor, teaching how to package Perl libraries in the context of Debian operating system. I use Arch btw.

## Learning how to package

In the [first tutorial](https://joenio.me/tutorial-pacote-debian-parte1/), we started understanding how is the package anatomy: together with the app source code, a Debian package contains a `debian/` directory, which has the copyright, control, changelog, rules and watch files. We used the [Acme::Helloworld](https://metacpan.org/pod/Acme::Helloworld) as a work case for this.

With a VM configured, the Debian Perl Group has a great [collection of tools](https://tracker.debian.org/pkg/pkg-perl-tools) to help us in the process of creating packages, automating much of the process. After following some procedures, we had to make some changes do the `debian/` files , run some checks and then build it, we used the pbuilder tool to do it. Everything went as expected, with no problems in the process. After this, its done! We have a package, and it can be installed using `dpkg`.

## Trying to contribute with a real software

After this first experience, we went to the [second tutorial](https://joenio.me/tutorial-pacote-debian-parte2/), in which we chose a real software and tried to package it. Me and [Felipe Aníbal](https://felipeanibal.github.io/sl/) decided to go with the [Date::Holidays::AW](https://metacpan.org/pod/Date::Holidays::AW) lib, which implements Perl functions to provide us the Aruba's official holidays! This is awesome.

We followed the same steps as done in the first tutorial, but this time we faced a problem: this lib has a dependency on the [Date::Holidays::Abstract](https://metacpan.org/pod/Date::Holidays::Abstract) and the [DateTime::Event::Easter](https://metacpan.org/pod/DateTime::Event::Easter) libs, that are not packaged in the Debian repos. We found this problem when running the `cme check` command, used to analyze the project in search of warnings and errors. This got us a bit frustated, considering the other activities we are working on, and we decided to step out of it without packaging any of this libs. You can find the last version of our progress [here](https://drive.google.com/file/d/1WB2tryD6D7NwYF-aXH-pQaxsQel3lhUT/view?usp=sharing).

## Conclusion

In the end we didn't send the package to the upstream, but I got surprised about how simple is the whole process, a way more easy than I though before. And this a great way of contribute to the community! I found great learning how to do it.

