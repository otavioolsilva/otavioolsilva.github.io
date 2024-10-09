---
title: A retrospect in MAC0470
date: 2024-06-24 20:00:00 -0300
categories: [Misc]
tags: [MAC0470]
---

You may have noticed that all the posts made so far ended with "This post was made as part of the discipline MAC0470 - Desenvolvimento de Software Livre, BCC - IME - USP". This is because all of them were made as part of... you got it. This discipline was ministred by the professor [Paulo Meirelles](https://www.ime.usp.br/~paulormm/) and aimed to provide to its students a first experience in the FLOSS world, with support of the professor, teaching assistants (TAs) and own classmates. The semester is ending and this a retrospective about all we did along it!

## Kernel

We started this semester being introduced to probably one of the hardest projects to contribute with: the Linux Kernel. And I don't say that because it's a hard project (in fact, it is, but at the same time I had the fell that contributing to it is more accessible than I thought before), but because it requires a great preparation and a knowledge of, besides the code, how to effectively contribute, like all the rules about formating and sending patches. In first workshop classes, we dedicated some time to prepare our local environment to, right after it, try to do our first contribution.

Me and [Felipe](https://felipeanibal.github.io/), my partner across all the semester, chose to work on a driver for an analog to digital converter (ADC) device from the Industrial I/O (IIO) subsystem, proposing a way to correctly handle negative values from the input in the differential mode of the device. We sent the patch to the mailing list but had a surprise: after investigating the device reference docs, we figured out that the driver we were working on didn't implent the mode in which the input can be negative. So, or we should implement support to this mode or our contribution wasn't going to be effective. The difficulty level of the first option was extremaly above ours, so we ended up leaving it aside.

Even if it didn't go as we expected, I think I learned a lot in this process. Working on a code that isn't mine and specially one in the magnitude of the Kernel was difficulty, but amazing. I think that was a great way to start this discipline, gave me a clear idea of how the FLOSS world works and that it's more accessible than I thought. The support from the professor and the TAs was also essential too and I think the workshops in class worked very well.

More can be seen in [this post about the environment preparation process](https://otavioolsilva.github.io/posts/setting-up-an-environment-to-contribute-to-the-kernel/) and in [this post about the contribution itself](https://otavioolsilva.github.io/posts/contributing-to-the-kernel/).

## Kernel Workflow (KW)

The second project we worked on was the Kernel Workflow (KW), a collection of scripts that aims to make easier some frequent tasks that a kernel developer deals with. And this time our contribution was accepted! We worked on an improvement to the list of available video connectors, informing which ones of them were being used. I think the biggest learning from this was about the contribution process: we received a lot of suggestions and requests of changes in our code, and it was great! In addition to learning some new bash commands, dealling with the maintainers was a great experience, and it was even more amazing when I received the mail informing that our push request was accepted. A great feeling!

More can be seen in [this post](https://otavioolsilva.github.io/posts/contributing-to-kw/).

## Debian packaging

Coding isn't the unique way to contribute to a FLOSS project. An example of this is packaging software to GNU/Linux distributions. More specifically, we learned how to do it for the Debian system. Even thought we didn't submit our package to the Debian repository because of some dependencies, this helped me to understand the different ways to contribute. I just think we had few time to work on this, considering the next phase of the discipline required a lot of our effort and we were a little late to start it. Maybe with some more time we could find another software to make a package and then submit.

More can be seen in [this post](https://otavioolsilva.github.io/posts/learning-debian-packaging/).

## GNOME

In the second phase of the discipline, we had autonomy to choose a project to contribute with. Me and Felipe chose to work with the GNOME project, as we are users of the GNOME desktop environment. Specifically, we chose to work with the GNOME Calendar app, as it has a solid community and a very organized GitLab, helping us to find how to contribute. In addition to that, the GNOME project offers a set of tools that helps their contributors, like Builder, an IDE designed to help working with de GNOME apps, and a lot of online docs, that really helps a lot.

In our first issue, we proposed an improvement in the way URLs are displayed in tooltips (the dialog box that appears when you hover the mouse over an event in the calendar). Normally, when the location of a event is a link, the URL is showed in the tooltip, but if the URL is big it can be very annoying, as it can get in the front of another useful information. So, we proposed to truncate the URL and to identify the meeting service provider, displaying it instead of the raw link. We sent the merge request and are now waiting for an answer from the maintainers. This was our first contact with anything from GNOME at the code level, and it seemed very feasible to work with! The community is also great.

In our second issue, we were searching for some time zone strange behaviors to be tested and ended up going deep in a bug: all-day events can appear with one day offset in the lateral menu, but it only occurs in some time zones. We found that it's probably a problem with the time zone offset when applied to the dates. We are also waiting for the maintainers answer. Working on it was trully amazing, as we had to take a close look to the code and investigate how the functions were working together.

More can be seen in [this first post](https://otavioolsilva.github.io/posts/contributing-to-gnome-1/) and in [this update on our progress post](https://otavioolsilva.github.io/posts/contributing-to-gnome-2/).

## Seminars and classes

In addition to all these projects, we also had many lectures with different themes in the context of the FLOSS world. I think they added a lot to the discipline, as we heard different perspectives of great professionals who works today with FLOSS projects and had a great networking opportunity as well.

# Conclusion

I think this discipline accomplished with success its goal! The experiences we had were diverse, dealling with different ways of contributing, some of the contributions being accepted and some not, different programming languages... And all of that was great! I think the biggest learning from this was how to deal with the community and to not be afraid of contributing, every attempt is welcome. The structure of the discipline worked really well, with balance between the projects and the lectures. The final result was very positive.

Just out of habit: this post was made as part of the discipline MAC0470 - Desenvolvimento de Software Livre, BCC - IME - USP.

