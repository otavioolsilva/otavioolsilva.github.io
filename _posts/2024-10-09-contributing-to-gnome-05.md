---
title: Contributing to GNOME\0x2C adjusting text encoding
date: 2024-10-09 14:00:00 -0300
categories: [Experiences with FLOSS, GNOME]
tags: [FLOSS, gnome-calendar, MAC0456]
---

I said in the [last post](https://otavioolsilva.github.io/posts/contributing-to-gnome-04/) that me and [Felipe](https://felipeanibal.github.io/) would probably work on one issue at a time, we ended up taking this too seriously. In this post, I will share the advances we've made on the issues we were aiming to face and the problems we encountered during this period.

## Files mysteriously breaking the import dialog, #1243

Probably the title of this post already gave a hint for you on this problem. When studying issues related to the import dialog, especially the ones related to the creation of events using virtual time zones, we found the [#1243](https://gitlab.gnome.org/GNOME/gnome-calendar/-/issues/1243) very interesting. As I related in the last post, the GNOME Calendar still can't handle virtual time zones, that are custom time zones defined directly in the iCalendar file, and this issue looked to be exactly this. However, there was a small difference: when importing an event that uses a virtual time zone using the file attached as example, the dialog breaks, different from the other related issues.

![Import dialog after trying to import an event from the attached iCalendar file]({{ '/assets/img/2024-10-09-contributing-to-gnome-05/broken-import-dialog.png' | relative_url }})
_For the file attached as example, the dialog don't show anything, not even the event in a wrong time zone as observed in other issues_

This was an interesting behavior. What was it about this file that made it so special? Trying to dissect this file by removing some parts and trying to import it has showed for me that its content weren't the problem, even removing the virtual time zone definition wasn't the solution. One thing brought my attention: whenever I saved the file in NeoVim after a modification the editor informed that the file was "converted". This raised an alert for me: maybe an encoding problem? And that was exactly the case: the GLib used in the GNOME Calendar is prepared to deal with strings in UTF-8, however this file was encoded in UTF-16LE, making the parse of the file to broke before reading anything relevant.

Me and Felipe invested some time understanding how characters are encoded and discovered that there isn't a standard way to identify the encoding of a text. There is a way, but it isn't a requirement to the files to have it: the byte-order-mask (BOM) is a special Unicode character placed in the beginning of the text that is used to indicate the byte order of the file and also to help identify which is the encoding used. So, relying on the BOM is almost a heuristic to solve this problem, as we can't design a solution that works all the times. We implemented an algorithm in MR [!496](https://gitlab.gnome.org/GNOME/gnome-calendar/-/merge_requests/496) that checks the first bytes of the data read and try to verify for the existence of the BOM to eventually parse the file to UTF-8, if necessary.

After implementing it, we made some tests to verify if the behavior of our code was the expected, but there was something strange: executing the Calendar using the memory leak detector (Valgrind) with our solution implemented led to an increase in the number of the definitely lost memory blocks. Again, time to dissect our code. Apparently the `g_convert()` function from GLib is the problem, but we still don't have great ideas of what is happening with it. I did some research in the [GLib repository](https://gitlab.gnome.org/GNOME/glib) trying to understand the flow of the allocated memory in the successive function calls after this one, but I couldn't see where was the error. I'm still having some difficulty to understand how can I modify the library and test it, and this makes the process even harder. For now, our solution works, but this is certainly a technical debt that needs to payed in the future.

## Work in progress

We are still working on the two fronts I mentioned in the last post:
 
- Improving the documentation of the tests: we're now trying to understand how Meson works and its pipeline of execution, to describe better how to implement tests without the necessity of GNOME Builder. Felipe has faced some problems with his development environment and this made this task a way harder, but we are figuring out how to deal with it.

- Virtual time zones: we're studying the possibilities of dealing with this topic, one good next step will be to look other implementations of this same behavior in other applications, such as Evolution.

This post was made as part of the discipline MAC0456 - Tópicos Especiais em Engenharia de Software, BCC - IME - USP.
