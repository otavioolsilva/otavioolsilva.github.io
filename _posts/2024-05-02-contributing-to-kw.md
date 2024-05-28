---
title: Contributing to the KW tool
date: 2024-05-02 11:00:00 -0300
categories: [Experiences with FLOSS, starting]
tags: [FLOSS, kw, MAC0470]
---

After [trying to contribute to the Linux Kernel](https://otavioolsilva.github.io/posts/contributing-to-the-kernel/), now it's time to try again to contribute to another FLOSS project: [Kernel Workflow](https://kworkflow.org/), or KW to the close ones[^joke-footnote].

## The KW tool

When developing to the Kernel, there are some frequent activities that could be automated, through scripts for example. This is the mission of the Kernel Workflow: to provide in a unique interface some useful tools to Kernel developers, such as making easier the build process and sending the patch through email to the correct maintainers and lists. I already said some words about in the post ["Setting up an environment to contribute to the Kernel"](https://otavioolsilva.github.io/posts/setting-up-an-environment-to-contribute-to-the-kernel/) too.

This project was born at the University of São Paulo by some Computer Science students and it's still maintained by some colleagues who study here and some who have already graduated, but it has already reached a much greater magnitude than being just a local project. Check out their [GitHub repository](https://github.com/kworkflow/kworkflow/) too!

## Contributing

After some time looking the issues listed in the GitHub repo, me and my partner [Felipe Aníbal](https://felipeanibal.github.io/#) chose to work on an [enhacement suggestion](https://github.com/kworkflow/kworkflow/issues/1096) to `kw drm` made by [Rodrigo Siqueira](https://github.com/rodrigosiqueira), one of the main maintainers in the project.

`kw drm` is a tool aimed to provide some facilities to interact with the DRM subsystem in the Linux Kernel. The Direct Rendering Manager (DRM) subsystem is responsible for interfacing with GPUs of modern video cards, making possible to the user to send commands to the GPU and send/get some data. The `kw drm` tool uses this API to make easier to perform some actions, such as the one I describe bellow.

The `kw drm --conn-available` lists all the connectors available in the target machine, like this (in my own machine):

```text
[local] Card1 supports:
 HDMI
 eDP
```

As suggested by Siqueira, we could improve this info by showing somehow the connectors that are in use, and this isn't exactly hard to do, because every one of this connectors has a file named `enabled` in his source path (`/src/class/drm/.`) that has the string "enabled" if it's in use or "disabled" in the other case. So, to make this work, we made a few changes to the `src/plugins/subsystems/drm/drm.sh` file in the KW source code:

- Given the path of a card, we check if it's in use by looking to the "enabled" file as mentioned and save it's result in a new variable called "connector\_enabled".
  - If the target is the local machine, this is very straight forward: using the `<` operator we can store the file's content in our new variable.
  - In a remote machine, we had to use the `cat` command and run it remotely by using the `cmd_remotely` function provided by the `kw remote` lib.
- With this info in hands, we just append a ' * ' to the end of the connector name if it's in use.

By doing this, the result is:

```text
[local] Card1 supports:
 HDMI
 eDP *
```

We also docummented it, by informing this new behavior in the man pages. And also made a few changes to this tool's test suite, adding the new output pattern and making some of the test devices enabled and the others disabled, so we can ensure our patch is working as expected. All of this can be seen in our [pull request](https://github.com/kworkflow/kworkflow/pull/1101).

## Update: pull request accepted!

Today (05/27/24) finally our pull request was [accepted](https://github.com/kworkflow/kworkflow/commit/3f68a797884be7636cc8561db30d5f6a235898bb) by the maintainers! We sent our initial contribution on 05/02 and in the last few weeks we were working on the feedbacks that Rodrigo Siqueira and David Tadokoro sent to us, in majority some coding style changes. But besides that, they sent us great recommendations to improve the way we were implementing the feature, in special:

- For every connector, when we were checking if it's enabled we were oppening a new ssh connection every time. This is clearly not optimal, but we were not aware of what was happening when we called the `cmd_remotely` function as described in the last section. We changed this behavior by using the `-exec` flag with the `find` command when getting the connectors list in order to get its state together with his name, so in all the final logic implemented we only make one ssh connection. This also simplified the way we were implementing the tests, possibiliting to use only static values in the mock `cmd_remotely` function.

- In the last section we showed an example of how we expected to be the result of our change. But one thing we didn't notice is that it wasn't really informative when we have more than one connector with the same name. Imagine that we have two HDMIs connectors, with only one in use: we would have an output with `HDMI *` and another `HDMI`, without differentiating them. So, for the final version, now the command shows the index of the connector, like bellow:

```text
[remote] Card1 supports:
 HDMI-1 *
 HDMI-2
 eDP
```

So, this was our journey! It took more time than we expected, but the feeling when receiving the email approving our contribution was awesome. It was a great experience overall, I think I learned a lot about bash scripts and I was really impressed by the attention the KW maintainers put in their project, keeping a close eye to every detail, this is inspiring.

## Conclusion

Bash script can be a little scary at a first view and before this I had never worked with this language in a big project, but it was cooler than I though it would be!

This post was made as part of the discipline MAC0470 - Desenvolvimento de Software Livre, BCC - IME - USP.

[^joke-footnote]: Does this joke makes sense in english? I really dont know. :)

