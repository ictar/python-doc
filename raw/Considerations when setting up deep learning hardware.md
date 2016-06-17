原文：[Considerations when setting up deep learning hardware](http://www.pyimagesearch.com/2016/06/13/considerations-when-setting-up-deep-learning-hardware/)

---

![dl_considerations_rack_03](http://www.pyimagesearch.com/wp-
content/uploads/2016/06/dl_considerations_rack_03.jpg)

In last week's blog post, I discussed [my investment in an NVIDIA DIGITS
DevBox](http://www.pyimagesearch.com/2016/06/06/hands-on-with-the-nvidia-
digits-devbox-for-deep-learning/) for deep learning.

**It was quite the investment, weighing in at a staggering (and wallet breaking) $15,000**_ _-- more than I ever thought I would spend on a computer that lives in my office (I normally like hardware that exists in the cloud where I don't need to physically interact with it).

That said, this is an investment that will drive the PyImageSearch blog over
the coming months. I've made the _commitment_ do to more _more deep learning
tutorials_ -- specifically tutorials that involve _Convolutional Neural
Networks_ and _image classification_.

In fact, over Memorial Day weekend, I spent two hours inside Trello
brainstorming and planning the next _year's worth_ of tutorials on the
PyImageSearch blog.

**Most of them involve deep learning.**

So, with that said, let's take a look at some considerations you should keep
in mind if you decide to purchase your own DevBox or build your own system for
deep learning.

## Considerations when setting up deep learning hardware

The NVIDIA DevBox isn't a "normal" piece of hardware like your laptop or
desktop. While you _can_ simply plug it into the wall and boot it up, you
really _shouldn't._

Before you even boot your system up for the first time, you need to consider
how you are going to handle power surges, battery backup, and where the box is
going to "live" in your home or office (this is _critical_ for keeping the
system cool).

While I'm writing this blog post in context of the NVIDIA DIGITS DevBox,
_these same suggestions will apply to you_ if you decide to build your own
deep learning system from the ground up.

### Get a UPS -- but not just any UPS

First of, you need to get an [Uninterruptible Power
Supply](https://en.wikipedia.org/wiki/Uninterruptible_power_supply) (UPS). As
the name suggests, a UPS provides emergency power to your system(s) when your
main power source fails -- you can think of a UPS as a large, (very) heavy
battery pack to keep your system running when the power goes out. A UPS can
even initiate a shutdown sequence once the battery backup levels are
critically low.

But before you run down to your local electronics store or hop on Amazon.com,
you need to ask yourself one critical question -- _**how much power will my
system be drawing?**_

#### The microwave test

At max load, the NVIDIA DevBox [power consumption can reach
1350W](http://docs.nvidia.com/deeplearning/digits-devbox-user-
guide/index.html#power) (this is due to the 4 Titan X GPUs in the system).

In the United States, 1350W is comparable to higher-end microwaves, so you
should perform what I like to call _"the microwave test"._

Check the wattage of your microwave, and if it's similar to the wattage draw
of the deep learning system you're looking to build, haul the microwave down
to the room where you plan on hooking up your deep learning system, turn it
on, and let it run for a few minutes.

If you don't overload the circuit after a few minutes, then you're _probably_
in a good shape.

I stress the term _probably_ because this isn't a perfect test. Running a
microwave of similar wattage for a _few minutes_ is not the same as running a
system for _days_ or even _weeks_ on end at high load. When running your deep
learning system, you'll be drawing _more power_ for _longer periods of time._

Therefore, it's critical to do further research and ensure your home/office is
wired properly for this sustained power draw. In some cases, you may need to
consult an electrician.

#### Death by RAID

You know what happens when you have a RAID array (Redundant Array of
Independent Disks) running and the power abruptly shuts off and your system
immediately shuts down?

**Bad things. Bad things will happen.**

Your data can be left in an inconsistent, "dirty" state, and your system may
need to create the entire array (if it can). As this _[Why power failures are
bad for your data](http://www.halfgaar.net/why-power-failures-are-bad-for-
your-data)_ article suggestions, RAID 0 can (potentially) be _completed
destroyed_ in a power failure.

Luckily, most modern Unix kernels and RAID tools have built-in precautions to
help prevent catastrophic loss during a power failure.

_But why take the chance?_

Invest in a UPS that can initiate a safe, clean shutdown in the event power
goes out and the battery levels are critically low.

_**Note:** Why am I talking about RAID? Because the NVIDIA DIGITS DevBox ships
with 3 x 3TB RAID5 disks with a separate SSD cache. The last thing I would
want would be to (1) rebuild that array or (2) lose my data due to power
failure._

#### The land hurricane

![Figure 1: A derecho, also know as a "land
hurricane".](http://www.pyimagesearch.com/wp-
content/uploads/2016/06/derecho_clouds.gif)

**Figure 1:** A derecho, also know as a "land hurricane", nearly destroyed every computer and hard drive I owned back in 2012 [[source](http://www.spc.noaa.gov/misc/AbtDerechos/derechofacts.htm)].

Every hear of the term **_derecho?_**

It's defined as _"a widespread, long-lived, straight-line wind storm that is
associated with a land-based, fast-moving group of thunderstorms"_
[[Wikipedia](https://en.wikipedia.org/wiki/Derecho)].

Derechos are extremely damaging thunderstorms capable of producing hurricane
force winds, tornadoes, heavy downpours, and flash floods. Some people even
refer to derechos as _**land hurricanes**_. These types of storms happen
primarily in North America, but have occurred elsewhere in the world with
substantially less frequency.

It's important to understand that these storms develop _quickly_. In some
cases, you may not even hear the thunder as the storm approaches -- it's
moving _that_ rapidly. Keep in mind that the _storm itself_ is moving at 60
MPH or higher -- most thunderstorms don't even have _gusts_ that reach 60 MPH.

During my first year of graduate work at UMBC, my home state of Maryland was
hit by a historic derecho on [Friday, June 29th,
2012](https://en.wikipedia.org/wiki/June_2012_North_American_derecho). This
storm was one of the most _destructive_, _deadly_, and _fast-moving_ storms in
North American History.

I was alone in my apartment and the time. And to tell you the truth, _**it was
one of the most frightening experiences of my life.**_

Within minutes, extremely high winds (&gt; 60 MPH with gusts approaching 100
MPH) knocked down power lines and toppled large trees.

Roads flooded.

Brutal ground lighting strikes flashed across the dark, ominous sky, followed
by nearly instantaneous, deafening thunder claps that relentlessly continued
from 10PM at night to 4AM the next morning.

28 people died during the storm as large trees fell onto their homes, crushing
them underneath.

When the storm was over, most of my home city of Catonsville, MD didn't have
power for over _6 days_. Some parts of Maryland didn't have power for _weeks_
-- an unheard of time living in the modern day United States.

So, why am I telling you this?

During the storm, one of the electrical lines outside my apartment building
was struck by lighting. The surge traveled through the line, to the apartment,
and into my UPS.

**My APC UPS died that night…**

_**…but my electronics didn't.**_

My laptops and hard drives were safe.

Ever since that night, I've always, _always_ [bought
APC](http://www.apc.com/us/en/) -- and I've made damn sure that any type of
sensitive equipment or important data are behind a UPS.

Sure, if the surge is strong enough, no surge protector can save you. But you
might as well do your damnedest to ensure you don't lose your equipment (or
more importantly, the data) during a thunderstorm (or any other freak act of
nature).

In this case, I only lost my UPS -- _I could have lost a lot more._

#### But not just any UPS will do

So, as I mentioned above, you should be utilizing a UPS that has:

  1. Battery backup
  2. Surge protection

That's all fine and good, but you also need to consider the wattage that
you're drawing.

**Most consumer level UPS' cap out at 900W** -- in fact, I have [this exact UPS](http://amzn.to/1O6xVna) that I use for my laptops and external hard drives that caps out a 865W.

Considering that I would need to draw up to 1350W at maximum load, I clearly
need an industrial, server-level UPS.

After doing a bunch of research, I found a bunch of UPS' that can easily
handle higher wattage and amps. I ended up going with the [APC SMT2200RM2U
UPS](http://amzn.to/1UiY6Dr), which is actually 2x the price of other UPS' I
was looking at -- but as I said, I have a trust and affinity with APC, so I
decided to spend the extra money.

This rack mount UPS can handle 1980W and 2200VA, more than enough for my
NVIDIA DevBox.

### Racking it all up

After I received my UPS, I performed a quick test to ensure that the NVIDIA
DevBox would work with it. Sure enough, it did (although this image is almost
embarrassing to post):

![Figure 2: Testing my NVIDIA DevBox with UPS
backup.](http://www.pyimagesearch.com/wp-
content/uploads/2016/06/dl_consideratons_orig_setup.jpg)

**Figure 2:** Testing my NVIDIA DevBox with UPS backup.

When I posted this image in a Slack channel with a few of my other
entrepreneur colleagues, my friend Nick Cook of [SysDoc.io](http://sysdoc.io/)
nearly shit a brick.

Nick quickly pointed out the problems of having a UPS sitting directly on top
of carpet, followed by the DevBox sitting on top of the UPS. He was absolutely
right -- I needed to rack it all up to help with cooling and to keep the
equipment safe.

So again, I went to do my research and ended up purchasing the [Tripp Lite 18U
SmartRack](http://www.newegg.com/Product/Product.aspx?Item=N82E16816228139). I
could have gone with a smaller rack, but I decided to:

  1. Allow myself to expand and include new systems in the future, if I so desire.
  2. Give the NVIDIA DevBox a little more "breathing room" for cooling.

_**Note:** The NVIDIA DIGITS DevBox is _not_ made to be rack mountable.
Instead, I bought [a heavy-duty shelf](http://amzn.to/1RQhohL), racked the
shelf, and then set the DevBox on top of the shelf._

The rack itself was delivered in quite the large cardboard container:

![Figure 3: My rack, still in the box.](http://www.pyimagesearch.com/wp-
content/uploads/2016/06/dl_considerations_rack_01.jpg)

**Figure 3:** My rack, still in the box.

Compared to my UPS and DevBox, the rack could easily include both, leaving
room for growth:

![Figure 4: The rack itself is more than large enough to house both the UPS
and DevBox.](http://www.pyimagesearch.com/wp-
content/uploads/2016/06/dl_considerations_rack_02.jpg)

**Figure 4:** The rack itself is more than large enough to house both the UPS and DevBox.

I then spent the afternoon racking the UPS and DevBox:

![Figure 5: Fitting the UPS and DevBox into the
rack.](http://www.pyimagesearch.com/wp-
content/uploads/2016/06/dl_considerations_rack_03.jpg)

**Figure 5:** Fitting the UPS and DevBox into the rack.

Which looks quite nice in the space:

![Figure 6: The rack, all locked up.](http://www.pyimagesearch.com/wp-
content/uploads/2016/06/dl_considerations_rack_04.jpg)

**Figure 6:** The rack, all locked up.

The DevBox will also serve as a nice heater during the winter months:

![Figure 7: My office, now that the NVIDIA DIGTS DevBox, UPS, and rack have
been fully installed.](http://www.pyimagesearch.com/wp-
content/uploads/2016/06/dl_considerations_rack_05.jpg)

**Figure 7:** My office, now that the NVIDIA DIGTS DevBox, UPS, and rack have been fully installed.

## Summary

In this blog post, I detailed considerations and suggestions you should keep
in mind when setting up deep learning hardware. Regardless if you are using
the NVIDIA DIGITS DevBox, or building a custom solution, make sure you:

  1. Determine if your house, apartment, or office is correctly wired to draw the required amps/wattage.
  2. Invest in a UPS to protect your investment, _especially_ if you're using RAID configured drives.
  3. If necessary, rack it all up.

Now that the deep learning environment is all ready to go, I'll spend next
week detailing how to install the CUDA Toolkit for deep learning and cuDNN so
you can take advantage of GPUs for speedy training.

_**Note:**__ The NVIDIA DevBox already comes with CUDA and cuDNN installed, so
I'll be setting up an Amazon EC2 instance from scratch. You'll be able to use
these instructions to stand up your own deep learning machine so you can
follow along with future tutorials._

**Be sure to sign up for the PyImageSearch Newsletter using the form below to be notified when future blog posts are published!**

