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

Some alternatives were given to us to make our first contribution and trying to understand what these proposals were about already was a great experience, diving into the Linux code and figuring out how the modules were designed was awesome. Me and my partner, [Felipe Aníbal](https://felipeanibal.github.io/#), choose to work on the driver for the [LTC2309](https://www.analog.com/en/products/ltc2309.html), a 12-bit ADC. When reading the raw input, this driver store the data on an unsigned 16-bit variable, the 4 least significant bits are just discarted and then the 12 most significant bits are returned in a 32-bit integer. However, when dealing with negative values from the ADC, we have to ensure that this new 32-bit integer is in the two's complement format, with all the bits before the real most significant bit (the 11th one) being only 1's. To do this, we can use de `sign_extend32` function, and this exactly what our patch is proposed to do. In the old version, there wasn't any way to ensure this, but our addition simply calls the function `sign_extend32` to garantee that the value in the return will be correct interpreted. You can find the [patch here](https://lore.kernel.org/linux-iio/20240420233836.24971-1-otavio.ols@usp.br/T/#u), which was made with the help of our friend [Thiago Duvanel](https://th-duvanel.github.io/), who was working on this same driver too.

The patch was send for an internal revision in the University context using the `git send-email` tool and, after some adjustments in the patch note, send to the IIO maintainer though the `kw mail` too, which is simplifies this process. Right now, we are waiting for his feedback.

## Update: the maintainer feedback

We sent the patch on 04/20 and right the next day we received an [answer](https://lore.kernel.org/linux-iio/20240421183820.4e2c0133@jic23-huawei/) from Jonathan Cameron, the IIO subsystem maintainer, faster than we expected (unfortunately our answer to him took a way more time). He made a question we weren't making for ourselves: can the values from this device really be signed? So, we ran to the [docs](https://www.analog.com/media/en/technical-documentation/data-sheets/2309fd.pdf). Studying it and with the help of [Gabriel Schwartz](https://kaos9001.github.io/), a friend from the class, we understood that this device provides both differential and non-differential data, and both of this modes can be used on bipolar and unipolar input modes. When working in unipolar mode, the device only consider one channel from input and both the non-differential and differential range of values is positive, apparently because in the differential the difference is taken beetween the input and the ground value, which is always zero. Only in the bipolar the differential values can be negative, because in it we will really take the difference beetween two different channels of input. The problem is the driver we were working on just implements the unipolar mode, so even supporting differential input, it will never be signed.

Our patch could be changed into a implementation of the bipolar mode to the driver, but this increases significantly the required experience with the IIO subsystem. As we dont have the device with us to effetively make tests and it would require a lot more knowledge than we have, we decided to [step back from it](https://lore.kernel.org/linux-iio/20240428020247.50114-1-otavio.ols@usp.br/). Even though, this was a great experience! And even that our patch wouldn't brake the Kernel, it was great to see that someone was carefully looking into what is added to the project and certifying to not introduce desnecessary things.

## Conclusion

Our contribution was small, but at the same time was a great opportunity to learn about the Kernel, modules and even the industrial ADC's world. Even though we were mistaken about the necessary modifications, it was a great experience to dive into this, and the maintainer was very helpful. It's amazing to see how the FLOSS culture encourages anyone to contribute with the softwares they use everyday, helping to make it even better.

This post was made as part of the discipline MAC0470 - Desenvolvimento de Software Livre, BCC - IME - USP.

