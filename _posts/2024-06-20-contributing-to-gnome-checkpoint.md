---
title: Contributing to GNOME, checkpoint
date: 2024-06-20 19:00:00 -0300
categories: [Experiences with FLOSS, GNOME]
tags: [FLOSS, gnome-calendar, MAC0470]
---

After some weeks studying and thinking on some [GNOME Calendar issues on GitLab](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues), this is a first report of our advances in this new project, following the roadmap described in the [last post](https://otavioolsilva.github.io/posts/contributing-to-gnome-roadmap/). 

## First week: studying the app and the first issue

During all my time using GNU/Linux I have used the GNOME Desktop Environment (DE), but had never looked at the code under the hood. So, to start contributing to it, not more plausible than look at the issues labeled with the 'newcomers' tag, and that was the greatest motivation for me and [Felipe](https://felipeanibal.github.io/) to choose the [#1232 issue](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1232). Futhermore, it was created by one of the maintainers of the project not so long ago and really seemed to be a feasible task, with clear instructions of the behaviour expected to be implemented.

The idea of the issue is: when creating an event on the Calendar you can set its location, which is exibited in the popover widget (the popup box that opens when you click at the event) and in the tooltip (the dialog that is shown when the mouse hovers over the event). In the popover, a cool feature is that when the location is a known meeting service link, its name is showed above the URL:

![Meeting service name above the URL](https://i.imgur.com/FgACqcw.png)

However, in the tooltip it isn't useful to display all the URL, as it can end up hiding other informations bellow the tooltip, especially when the link is big, and it isn't even clickable:

![Tooltip can be annoying](https://i.imgur.com/VgFdI6f.png)

So, why not to reuse the function that parses the URL in the popover to display the meeting service provider name in the tooltip instead of the link? We have work to do!

The first task was to reproduce the current behaviour and try to understand at code level what was happening, and this was the goal for this week. The tooltip to an event is generated in the `gcal_event_widget_set_event_tooltip` function in the [gcal-event-widget.c](https://gitlab.gnome.org/GNOME/gnome-calendar/-/blob/main/src/gui/gcal-event-widget.c?ref_type=heads) file, and the point that really interest us is when the location is defined:

```c
    /* Append event location */
    if (g_utf8_strlen (gcal_event_get_location (event), -1) > 0)
    {
      g_autofree gchar *escaped_location;

      escaped_location = g_markup_escape_text (gcal_event_get_location (event), -1);

      g_string_append (tooltip_mesg, "\n\n");

      /* Translators: %s is the location of the event (e.g. "Downtown, 3rd Avenue") */
      g_string_append_printf (tooltip_mesg, _("At %s"), escaped_location);
    }
```

So, our job was to reimplement the way the location is inserted into the tooltip in the code snippet above to follow this new proposed behaviour. But how to deal with the location when the link is to a unknown meeting service? And when the location is a real place, not a URL? We made a [comment](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1232#note_2125426) in the issue in GitLab to clarify this points and to express our intention to work on this issue, this being our first contact to the maintainers in the project.

## Second week: implementing, but not submitting

The [answer](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1232#note_2125507) from [Jeff Fortin (@jfft)](https://gitlab.gnome.org/jfft) came fast: when the URL is from a unknown service or from a real location, we decided to just keep it as it is, but in the case of a URL we trucante it at 50 characters to not pollute the screen.

So, considering the code snippet above, we just needed to change the logic of how the location string is constructed, adding some more steps before appending it to the tooltip message:

- First, we need to know if it is a URI or just a plain text (like a real address), and that can be done though the `g_uri_parse` function.
- If it's a URI, we try to identify if it's from a known meeting service provider, like Google Meet, Zoom or another one. This can be done in the same way it's done in the popover. If it's unknow, we just truncate the URI length at 50 chars.
- After this, or if it isn't a URI, we escape the text and add it to the tooltip message.

Alright, seems to be a good flow, but how the popover discovers the meeting service provider name? This turned out to be a little problem: the function used to do it (`get_service_name_from_url`) is defined locally in the [gcal-meeting-row.c](https://gitlab.gnome.org/GNOME/gnome-calendar/-/blob/main/src/gui/gcal-meeting-row.c?ref_type=heads) file. As we are now needing to use it in another context, nothing more reasonable than move it to the [gcal-utils.c](https://gitlab.gnome.org/GNOME/gnome-calendar/-/blob/main/src/utils/gcal-utils.c?ref_type=heads) file. We asked the maintainers about it and are still waiting for confirmation of [Georges Stavracas (@feaneron)](https://gitlab.gnome.org/feaneron).

## Third week: the second issue, we went too deep

As the first issue went well, while waiting for the maintainers answers we decided to search another things to do. Looking at the [@jfft personal blog](https://fortintam.com/blog/) I ended up getting into this [post](https://fortintam.com/blog/call-for-help-writing-gnome-calendar-compliance-unit-tests/), which is a call for the community to contribute expanding the Calendar test suite. Me and Felipe thought it was a great opportunity to discover more about how this app works, as we would need to dig in the code to find its weak points, and at the same time contribute to make it more solid.

That said, lets find something to test! We started following the suggestion of @jfft: looking at the issues labeled with 'Timezones' (apparently, [timezones are kind of scary](https://youtu.be/-5wpm-gesOY?si=RwNWry15RWKjaKkj)). After looking at some of them, we found the [#1202](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1202) really interesting: 'all-day events' sometimes was offset by one day in the sidebar for no clear reason. Looked easy to test: we would just need to compare the real date of the event with the one being exhibited in the sidebar. But how could we get these dates? We knew we would need to dig a little in the code to find them, but we went a bit deep: we ended up trying to understand the whole problem, why it was happening and causing the dates to not match. And I think we made progress! You can find our observations on [this commentary](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1202#note_2140144).

I think this issue was a great exploration exercise for us, but all we reported in the commentary was just thoughts, we are now waiting for the maintainers to check if we went to the right direction and to provide some clues on how to workaround this problem. And after that, we maybe finally get into make some tests.

## Now

As I'm writing this, we are waiting for the maintainers to answer both of the issues. In the first issue, we already implemented locally a way to provide the expected behaviour and are waiting the @feaneron confirmation to then send the merge request. In the second, we are waiting to get some thoughts on how to proceed. I think waiting is part of the experience too. Our roadmap expired, but we will keep working on these issues and maybe on some more, it has been a great experience! More updates in a next post.

This post was made as part of the discipline MAC0470 - Desenvolvimento de Software Livre, BCC - IME - USP.

