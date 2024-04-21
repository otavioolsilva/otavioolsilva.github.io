---
title: Contributing to the Kernel
date: 2024-04-20 23:00:00 -0300
categories: [Experiences with FLOSS, starting]
tags: [FLOSS, IIO, kernel, linux, MAC0470]
---

In the [last post](https://otavioolsilva.github.io/posts/setting-up-an-environment-to-contribute-to-the-kernel/) I learned how to deal with VM's, build the Linux Kernel and configured my environment. Now, it's time to make use of this knowledge and finally make a contribution to the Kernel. Or at least, try to make one.

## The IIO subsystem

The Linux Kernel is divided into a lot of subsystems, and each of them has a designated maintainer. The first stop for a contribution is these maintainers, who review the suggested patches and pass them to the upper-level of the chain, until they reach the mainline Kernel repository. So, to make a contribution to the Kernel, the first thing to do is choose a subsystem: in my case, the IIO subsystem.

The Industrial I/O subsystem (or IIO) provides support for devices that in some sense perform analog to digital conversions (ADC) or digital to analog (DAC). A lot of devices fall into this category, like accelerometers, sensors, and the ADC's themselves. More info can be found at the [Linux Kernel's Docs](https://www.kernel.org/doc/html/v4.12/driver-api/iio/intro.html). The git repo to the IIO tree can be found [here](https://git.kernel.org/pub/scm/linux/kernel/git/jic23/iio.git/). This subsystem was chose because, despite not having all the equipament here at the University to test the patches, the local FLOSS group of study ([FLUSP](https://flusp.ime.usp.br/)) has some experience working with it and already has some [great tutorials](https://flusp.ime.usp.br/kernel_iio/) introducing the subsystem and proposing some experiments.

## Contributing

Some alternatives were given to us to make our first contribution and trying to understand what these proposals were about already was a great experience, diving into the Linux code and figuring out how the modules were designed was awesome. Me and my partner, [Felipe Aníbal](https://felipeanibal.github.io/#), choose to work on the driver for the [LTC2309](https://www.analog.com/en/products/ltc2309.html), a 12-bit ADC. When reading the raw input, this driver store the data on an unsigned 16-bit variable, the 4 least significant bits are just discarted and then the 12 most significant bits are returned in a 32-bit integer. However, when dealing with negative values from the ADC, we have to ensure that the two's complement bit is at the index 11 while changing the variables sizes. To do this, we can use de `sign_extend32` function, and this exactly what our patch is proposed to do. In the old version, there wasn't any way to ensure this, but our addition simply calls the function `sign_extend32` to garantee that the value in the return will be correct interpreted. You can find the [patch here](https://lore.kernel.org/linux-iio/20240420233836.24971-1-otavio.ols@usp.br/T/#u), which was made with the help of [Thiago Duvanel](https://th-duvanel.github.io/), who was working on this same driver too.

The patch was send for an internal revision in the University context using the `git send-email` tool and, after some adjustments in the patch note, send to the IIO maintainer though the `kw mail` too, which is simplifies this process. Right now, we are waiting for his feedback.

## Conclusion

Our contribution was small, but at the same time was a great opportunity to learn about the Kernel, modules and even the industrial ADC's world. It's interesting to see how the someone with no background in the industrial devices' world can still contribute to it, and this applies to the other subsystems and projects, it's amazing to see how the FLOSS culture encourages anyone to contribute with the softwares they use everyday, helping to make it even better.

This post was made as part of the discipline MAC0470 - Desenvolvimento de Software Livre, BCC - IME - USP.

