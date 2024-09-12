---
title: Contributing to GNOME, timezones and more timezones
date: 2024-09-10 17:00:00 -0300
categories: [Experiences with FLOSS, GNOME]
tags: [FLOSS, gnome-calendar, MAC0456]
---

In the [last post](https://otavioolsilva.github.io/posts/contributing-to-gnome-3/), me and [Felipe Aníbal](https://felipeanibal.github.io/) have made some great advances, and I think it wasn't different this time (or maybe I'm just accumulating too much content for one post). Our expectations were to keep looking at issues from the GNOME Calendar related to timezones, specially trying to expand the test suite on it, as described in the epic issue [#1093](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1093). We submitted patches for two of the issues listed and we're also working on some others of them, lots of work to do!

Also it's worth to point out that the [GNOME 47 is very close to be released](https://release.gnome.org/calendar/)! We're currently at the 47.rc version (release candidate) and the 47 newstable release is planned for September 18th. The merge requests we sent so far will be available on the stable version of the GNOME Calendar in this release! 🥳

## Format error in the import dialog, #1175

There is some ways to add events to the GNOME Calendar and one of them is importing an event from an iCalendar file (.ics), whose format is described in the [RFC5545](https://datatracker.ietf.org/doc/html/rfc5545). When importing the event, the Calendar will ask you to confirm the action by showing a dialog listing all the events to be added:

![GNOME Calendar import dialog](https://gitlab.gnome.org/-/project/198/uploads/4322f60a26da8af98aba0f47766c02de/image.png)
_Import dialog displayed when importing a new event_

Some problems with it were related in [#1175](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1175). Note that in the picture above the start time of the event is 12:30 PM and the end time is 01:30 PM. The iCalendar file used to import this event have defined the start time to be 12:30 PM in the UTC timezone and the end time 01:30 PM also in the UTC timezone, but the screenshot was taken in the -05 timezone! So, the first problem is that the dialog was showing UTC events without converting them to the local timezone before displaying them. Fortunately, the [GLib](https://docs.gtk.org/glib/index.html) (a very useful library used as the low-level core for many projects such as GNOME and GTK) has a function that easily makes this conversion: [g_date_time_to_local](https://docs.gtk.org/glib/method.DateTime.to_local.html). Given a date in the correct struct, this function converts it to the local timezone, and we used it to fix the observed behavior.

That wasn't the only problem: also note that the start and end dates were being displayed in the 12-hours format (with AM/PM), and this was the behavior even if the user configured its system to prefer the 24-hours format. So, when constructing the string to be showed, we first needed to check the system preference for the time format. However, this information is keep in a GcalContext struct, that we didn't had access in this specific function. To handle this, we had to make it available by passing this struct as an argument to the function that creates each one of the entries in the dialog. After that, we just did a switch case to use the correct format.

This changes were submitted in the merge request [!463](https://gitlab.gnome.org/GNOME/gnome-calendar/-/merge_requests/463) and already merged, with this being the final result:

![GNOME Calendar import dialog fixed](https://i.imgur.com/v4IOmY7.png)
_The correct behavior_

Remember, however, that we found this issue in that epic issue that asked for improvements on the test suite. And where is the test for our change? The problem is that it would require us to test UI stuff, checking if the text exhibited was correct, and this can be very complicated. So, for the moment, we just sent the fix without the test. 

## A curious drag and drop bug, #1198

Another interesting issue ([#1198](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1198)): specifically in the week where the switch of timezones for daylight savings occurs, when dragging and dropping an event to change its date/time in the Calendar its start and end times would be shifted by one hour, as you can see in the video bellow (click to view it, credits to [Jeff](https://gitlab.gnome.org/jfft)):

![A video reproducing the issue](https://gitlab.gnome.org/-/project/198/uploads/cd3ebe90a465619a2c427ce3e91eff87/GNOME_Calendar_46_weekview_timetable_drag-and-drop_event_moving_during_week_of_the_DST_switch.webm)
_Events are being displayed with an one hour offset, click above to view the video_

This was the first time we were trying to make a change so close to the UI, but we had a great clue on what to do: [@danigm](https://gitlab.gnome.org/danigm) faced a very similar issue, the difference was that the shift happened during the event creation (as you can see [here](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/482)). And in [his commit](https://gitlab.gnome.org/GNOME/gnome-calendar/-/commit/6afb5cf3a5447e30486a37b6049a468c4399a0dd) he didn't only solved the problem but also created an util function to be called in other contexts that this problem happened. The problem is that the calculation of the selected date was wrong, and this function corrects it.

So, we just changed the code that was related to the drag and drop action to use the Daniel function and it worked out-of-the-box! We [submitted](https://gitlab.gnome.org/GNOME/gnome-calendar/-/merge_requests/491) it and already was merged after Jeff's tests. We also didn't submitted tests for this one because it would require testing the UI, and in an even more complex way since it's related to the movements of the user's mouse cursor. 

## Work in progress

We are currently working on some other issues that are also related to the epic issue about the timezone test suite cited in the introduction:

### More for the import dialog

Looking at the list of issues, [#1221](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1221) caught our attention: an event with the timezone explicit defined in the .ics file is being created as being in the UTC timezone. However, the problem seems to be a little more complex: the chosen timezone (EDT) isn't defined in the majority of systems, and so the Calendar just chose to use the UTC one. But this is the appropriate behavior? What should we do in this case? We discussed a little about this in the matrix chatroom, but we still need to figure out how to deal with this.

Also, the issue [#1110](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1110) seemed to be very similar to this one described above: timezones being used but the event was being incorrectly imported. In this case, the timezone is defined in the .ics file as a virtual timezone, with the explicit time offsets. Currently, the Calendar is not able to interpret it correctly and just displays the event as being in UTC.

Not only these two above, but the [#1243](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1243) also seemed to have a related problem: some .ics files with virtual timezone not only are being incorrectly interpreted, but also breaks the import dialog. Looking more close to this issue, me and Felipe figured out that the problem was happening for a different reason than the virtual timezone on the file: the encoding used for the example file in the issue is UTF-16LE and the GLib usually works with UTF-8, and apparently this leads to a problem during the parse of the file if an explicit conversion isn't made.

So, all this three issues are somehow related and we are working on them! Probably one at a time. :)

### Improving the docs related to the test suite, #1284

During the time we were studying the issues above, Jeff created a new issue ([#1284](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1284)) about the test suite: not only to expand it, but it would also be great if there were a more clear documentation about how the tests are made and how to execute them.

Our experience so far with this topic wasn't exactly vast: when we created a test for the issue [#171](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/171) we just looked at how the others were implemented and tried to follow the pattern, and the [GNOME Builder](https://apps.gnome.org/Builder/) runs the tests just with the click of a button, without any difficulty. But this could be a great starting point for creating this new documentation! We will think more about it.

This post was made as part of the discipline MAC0456 - Tópicos Especiais em Engenharia de Software (yes, a new discipline!), BCC - IME - USP.
