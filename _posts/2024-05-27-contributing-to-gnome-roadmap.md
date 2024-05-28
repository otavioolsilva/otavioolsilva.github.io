---
title: Contributing to GNOME, our roadmap
date: 2024-05-27 19:00:00 -0300
categories: [Experiences with FLOSS, GNOME]
tags: [FLOSS, setup, gnome-calendar, MAC0470]
---

Our [contribution to the KW tool was accepted](https://otavioolsilva.github.io/posts/contributing-to-kw/#update-pull-request-accepted), so it's time for a new challenge! Me and my partner [Felipe Aníbal](https://felipeanibal.github.io/sl) decided to keep contributing now with the [GNOME](https://www.gnome.org/) project, the desktop environment (DE) we use daily. What a better incentive than this one?

## Choosing the project

GNOME is a DE that groups a lot of smaller projects, as it can be seen in their [GitLab](https://gitlab.gnome.org/GNOME). So, to contribute to GNOME, you don't need to directly contribute to the desktop project. A great way we found to understand how we can contribute to it is by their [welcome page](https://welcome.gnome.org/), which lists a lot of apps that you can work on and how you can do it, like improving code, translation, docs and other suggestions. In this site you can find their [Code of Conduct](https://conduct.gnome.org/) too.

After studying and considering some projects, we decided to go with the [Calendar app](https://welcome.gnome.org/en/app/Calendar/). It's built with the C programming language, which we are familiar with, and uses the GTK library to provide the user interface. Besides that, another big reason to why we choose this app is because of its long list of [issues](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues) in GitLab, many of which are recent and with the "Newcomers" tag. Its development community seems to be very active too.

## Setting up the environment

To work on the app, we will use [Builder](https://wiki.gnome.org/Apps/Builder), an IDE created to make easier working on the GNOME environment. It has some great features that can help us, but in special it can easily build and run the Calendar app with the modifications we will make.

## Roadmap

Our roadmap for the next few weeks is this one:

- From 05/27 until 06/02: setup the local environment, study the GNOME Calendar app code structure and make the first contact with the maintainers, especially discuting this [issue](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1232);
- From 06/03 until 06/09: work on the chosen issue and submit the first merge request;
- From 06/10 until 06/16: keep working on the above issue or on a new one;
- Posteriorly: end pending tasks and finalize documentation.

The issue cited above seems to be a great one to start our journey and we expect it won't be the only one we will work on. By the weeks go on, I hope to make some more posts about our progress.

This post was made as part of the discipline MAC0470 - Desenvolvimento de Software Livre, BCC - IME - USP.

