---
title: Contributing to GNOME, trying to differentiate wrong time zones and normal ones in the Calendar import dialog
date: 2024-12-04 10:00:00 -0300
categories: [Experiences with FLOSS, GNOME]
tags: [FLOSS, gnome-calendar, MAC0456]
---

In the [last post](https://otavioolsilva.github.io/posts/contributing-to-gnome-06/) reporting the contributions [Felipe](https://felipeanibal.github.io/) and I did to the [GNOME Calendar](https://apps.gnome.org/en/Calendar/) project, we proposed some possible next steps and chose to follow different paths to close out this semester: I chose to keep working on the import dialog and Felipe chose to improve the documentation. We expected to invest more time in these choices in the last few weeks, here I report the progress we were able to make and also provide a brief review of our contributions in this last semester that is now ending.

## Even more in the import dialog

In the last post I reported that we changed the way the import dialog calculates the time offset for virtual time zones, making some adjustments in the util function `gcal_date_time_from_icaltime` in order to use the raw offset information to provide the correct time zone for the final displayed date. However, one issue remained: when importing floating time events (that don't have any time zone associated to it), the expected behavior would be to assign it to the local time zone (as the rest of the Calendar does[^1]), but the import dialog is interpreting it as in UTC.

One first thing to keep in mind is, as discussed in the last post, the import dialog don't use the same functions and logic to generate the events as the rest of the Calendar: we're just viewing a preview and don't want to keep any cached information, so we construct a local structure that can be erased without side-effects. So, it's no surprise that the behavior of the import dialog is different than the already imported events.

Investigating it closely, the point is that the util function cited before interprets floating time events and events with incorrect time zone (because of a typo, for example) in the same way, attributing both to UTC, different from the events already imported that are set as UTC only in the last case. The problem is that it isn't a fault of this function: as its name suggests, it receives an ICalTime struct and returns a GDateTime, however both floating time events and the ones with a wrong time zone have a undefined time zone in the ICalTime argument, so it's not possible to differentiate them at this point.

Having explained the problem, I haven't been able to think on a simple solution during these last few weeks that preserves the function signature (since it's used on another contexts). Perhaps we could handle with this specific case outside it, but would we do this on every call? It doesn't seem plausible. It's a work in progress issue.

## Advances on the docs

Our motivation for this topic is the [#1284](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1284) issue, in which Jeff Fortin, one of the maintainers of GNOME Calendar, asks for help to document the process related to the automated test suite. Felipe made advances in studying how the Calendar can be built manually and how to execute the tests through the command line, using the Meson build system. Soon we expect to synthesize this information and document it, to permit more people to contribute to the test suite.

## A brief semmi-anual retrospective

In this semester, I published four posts (counting this one) and they approached different issues (curiously the most of them related to the import dialog):

- In post ["Contributing to GNOME, time zones and more time zones"](https://otavioolsilva.github.io/posts/contributing-to-gnome-04/), issue [#1175](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1175), reporting some problems with the date and time in the import dialog, and [#1198](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1198), a drag and drop bug.

- In post ["Contributing to GNOME\0x2C adjusting text encoding"](https://otavioolsilva.github.io/posts/contributing-to-gnome-05/), issue [#1243](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1243), a very interesting problem with the text encoding of the files being imported by the Calendar.

- In post ["Contributing to GNOME, adjusting time zones in the import dialog"](https://otavioolsilva.github.io/posts/contributing-to-gnome-06/), still in issue #1243, but now focusing in the parsing of the time zone offset for VTIMEZONEs.

- This one, in which I report advances again on the parsing of time zones in import dialog and in the test suite documentation.

It was a great semester, with a lot of learning and I remain very motivated to keep contributing, not only to the GNOME Calendar, but to open source software in general. It's been a pleasure to take part on this community :).

This post was made as part of the discipline MAC0456 - Tópicos Especiais em Engenharia de Software, BCC - IME - USP.

[^1]: It's worthy mentioning that this behavior is the standard defined by [RFC5545](https://datatracker.ietf.org/doc/html/rfc5545) and was implemented a few months ago by Felipe and me [here](https://gitlab.gnome.org/GNOME/gnome-calendar/-/commit/5ce7b210dce80de17f1802fb1ccbaee261935cf3) :).
