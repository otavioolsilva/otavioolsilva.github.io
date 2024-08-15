---
title: Contributing to GNOME, great advances!
date: 2024-07-13 20:00:00 -0300
categories: [Experiences with FLOSS, GNOME]
tags: [FLOSS, gnome-calendar]
---

Finally the next post announced in the ["Contributing to GNOME, checkpoint"](https://otavioolsilva.github.io/posts/contributing-to-gnome-2/) is here, the last few weeks have been a bit intense working on the GNOME Calendar project, we did some great advances! At that moment, me and [Felipe](https://felipeanibal.github.io/) sent a merge request to the maintainers in the first issue ([#1232](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1232)) and were waiting their feedback. In the second issue ([#1202](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1202)), in which we just did some observations on the problem, we were just waiting too. From that point, a lot has happened.

## The first issue, #1232

After we sent the [MR](https://gitlab.gnome.org/GNOME/gnome-calendar/-/merge_requests/453), [Jeff](https://gitlab.gnome.org/jfft) answered us suggesting a small linguistic change in the string we were modifying and [Georges](https://gitlab.gnome.org/feaneron) also requested some changes in the code, the biggest part being only about coding style. We followed all the suggestions and, finally, [our MR was merged](https://gitlab.gnome.org/GNOME/gnome-calendar/-/commit/f3359d0246a413727277da88d4d193b3a002882b)!

Our modifications will take part in the GNOME 47 stable version, but right now this is the behaviour when building from the [GitLab repository version](https://gitlab.gnome.org/GNOME/gnome-calendar):

![Identifying meeting service](https://i.imgur.com/uSkuS3p.png)
_Tooltip displaying the meeting service name_

![Link truncated](https://i.imgur.com/tV1vEzu.png)
_When the meeting service is unknown, the link is now truncated at 50 characters_

When the location is not a URI, it's displayed as usual in its full length.

## A wild issue appears, #171

Some days before our MR being merged in the last issue, Jeff asked me and Felipe if we would have interest in a "presumably easy, but incredibly high-impact" bite-sized challenge (his words). We, of course, accepted it! This was the issue ([#171](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/171)) he was talking about: when importing a .ics file to the calendar, the dates can have a explicit timezone or not, and the Calendar was wrongly assigning the UTC timezone to the latter case, as the [RFC5545](https://datatracker.ietf.org/doc/html/rfc5545), which defines the iCalendar format, explictly says that events without timezones should not be bound to any one in particular, but stay in the local one.

The issue was created six years ago and some devs had already sent some patches to it, but they were never merged. The correction was pretty simple, just a one-line change, but Jeff also asked us to make add a new test to ensure this would work and not break anything else (also to make it more robust). So, [we did it](https://gitlab.gnome.org/GNOME/gnome-calendar/-/merge_requests/460)! Felipe and me just followed the correction the other devs had alredy suggested, made a new test to check if the timezone was being correct set in events and sent the MR. Again we made some coding style mistakes, but after fixing them, Georges merged it. This was a great way to understand how tests works and I feel much more confortable proposing some more!

## The (old) second issue, #1202

The last issue clarified for us how the Calendar handles timezones and brought us some ideas on this one ([#1202](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1202)): right now, all-day events have its timezone set to UTC, but this looks to be a problem, as they are converted to the local timezone before being displayed on the lateral menu and the timezone offset makes it to be wrong by one day in some cases. So, we made a commment in the GNOME Calendar Matrix channel bringing up this discussion, if assigning the UTC timezone is really the correct way to do it. We are now discussing it and investigating some related issues to see if we can have some new ideas on how to deal with it.

## Now

I was expecting to come in this post with just one MR, a lot of progress has been made. We are now talking with the other devs in the Matrix chatroom about the last issue and figuring out how to deal with it. We are also planning to study some other timezone related issues to propose new tests, as this looks to be a [important thing](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1093) to the project. I think that is it, happy with all of it and very excited for the next contributions.

This post wasn't made as part of the discipline MAC0470 - Desenvolvimento de Software Livre, BCC - IME - USP, its just to keep our progress reported somewhere :) .

