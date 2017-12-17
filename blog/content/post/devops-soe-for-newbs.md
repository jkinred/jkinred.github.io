---
title: "DevOps Vagrant Box"
date: 2017-11-26T15:55:05+11:00
draft: false
---

In this article I'll describe a successful solution to a lingering problem we
faced: Getting "traditional" Ops people working with modern infrastructure
tooling.

<!--more-->

_**Note:** This article was written retrospectively and we had started replacing
this with a set of Docker containers which we called Microtools in the spirit
of Microservices_

# Just show me the code!

The solution utilised Fedora Workstation slimmed down with Kickstart, Packer to
build the image, Masterless Puppet to configure the box and Vagrant to run it.

Unfortunately, I can't publish the code at this time.

# Overview

At the outset, I'll say that this is by no means a technical masterpiece.
Rather, I write this article to highlight an ability to overcome barriers that
can and do cripple a companies adoption of modern tooling.

There's also a social aspect to this, as I was criticised (respectfully) by
some peers for making it "too easy" for the receiving team to do DevOps. While
I get the point, I don't stand for technical elitism and respectfully counter
that things can never be easy enough!

# The problem

If you were doing infrastructure as code in a complex environment through the
Ruby phase you'll know the pain that could be involved with getting a
development environment right. In a large enterprise with many teams and
products at different stages of their lifecycle that problem increases
exponentially. Ruby versions, rbenv, gems, Puppet versions, corporate proxies
and you haven't even started unit testing your manifests.

The trivial need to have operators running a DevOps toolkit ran straight into
the kind of ludicrous thing that can happen in enterprises.

* Business throws many millions at "DevOps"
* Business keeps development and operations separate
* Operations must run enterprise SOE or risk being dismissed (culture of fear)
* SOE is Windows 7 32-bit! In 2014! Not the primary choice of elite DevOps

As our overall goal was to have our production infrastructure and deployments
fully automated there were calls to short circuit training up the operations
team and just throw the code over the fence to be run. Hmmm, sounds
suspiciously like what we're trying to avoid!?

I'd spent a lot of time with our operations team and they were really nice
people and competent system administrators. If we were going to do this right,
they had to be along for the ride and be able to inject their own changes into
the platform lifecycle.

So while super cool people are out there talking at conferences about the next
big thing, someone has to work out how to engineer around crazy problems like
this so customers can actually receive the benefits of the last big thing.

# Some solutions

* Run a terminal server? Operators work remote after hours
* Run your own VM? Even though the laptops have 8GB or 16GB of RAM, the 32-bit SOE restricts them to 4GB memory!
* Buy them all MacBooks? Not yet

# The solution

So now, any self-respecting engineer has to smile and play the long game here.

The solution consisted of the following technologies:

* Fedora Workstation (optimised for minimal size and memory use)
* Packer
* Puppet
* VirtualBox
* Vagrant

There were some other boxes about but they weren't lightweight enough for the
aforementioned problems and the OS was the easy part, getting the tooling
configured was the hard part.

### Fedora Workstation

I chose Fedora as it is the distribution I run on my own workstation. It was
easy to slim down the installation through Kickstart and use a minimal GUI
(XFCE).

### Packer

I absolutely love Packer and have used it for building development test images,
production VMware templates and Amazon Machine Images. Will probably do another
article on that.

### Puppet

Packer passed control to Puppet which we ran locally against the manifests
checked out from Git.

The manifests configured things such as:

* Standard tools and environment
* Editor environment for code compliance checking
* Corporate proxies (worth mentioning as I've seen so many competent tech people crushed by getting tooling to work behind these)
* Several Ruby versions with rbenv
* SSH authorized keys for elaborate jumping through platforms
* etc.

Operators could also checkout the manifests from Git at any time and align
their DevOps SOE.

### VirtualBox and Vagrant

All users had to do was install Vagrant and run:

```
vagrant box add <url>
vagrant up
```

# Profit!

The DevOps SOE was a success and used by many operators and subsequently
adopted by other teams as they encountered the same issue and exhausted
solutions.

Later on, maybe 18 months later, MacBooks started appearing. It would have been
a long wait :).
