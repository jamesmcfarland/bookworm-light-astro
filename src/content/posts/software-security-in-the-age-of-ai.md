---
title: software security in the age of ai
meta_title: ""
description: ""
date: 2026-04-10T11:59:00.000Z
image: "/images/zdenek-machacek-mUSuM4qdZi4-unsplash.jpg"
categories: []
authors: ["James McFarland"]
tags:
  - ai
  - security
draft: false
---

So this has been written in response to the Claude Mythos news; however, its implications are for most of the wider software industry and security going forward.

Claude Mythos

The Mythos model is a jump in capability we’ve not seen in a while, and I’d recommend reading through the system card, as I don’t want to rehash its claims or notes in here. In essence, it’s a highly capable model which, like state-of-the-art (SoTA) models before it, lowers the barrier to entry to technical tasks. This time, that barrier is the one that surrounds security, penetration testing, and vulnerability research.

We’re at the point where, costs aside, a fully autonomous system can run attacks against a particular target, working on different facets in parallel and meticulously without stopping.

There’s a few different areas within this news and topic to break down.

The benchmarks

An important caveat is that, whilst Anthropic have released very promising benchmarks, this model is only available to private groups at this time, so we cannot independently verify their benchmarks or indeed the alleged performance of this model. However, it appears to be a large but natural stepping stone and a capability which the industry is going to have to come to grips with going forward.

The private model

The model being held back is rather alarming to me. Anthropic feel they need to be the ones to hold it back in line with their own constitution, doing AGI safely, and in principle they are right. The issue with this is that AI capabilities proliferate fast. OpenAI, Google, etc. are never more than a few months behind, and it is probable they already have a model like this. Hiding behind closed doors and private groups protects things in the short term, but does nothing to address the longer-term concerns that are on the table. We should have the model’s capabilities laid bare so we as engineers can begin to adapt and build our future systems to not only handle the threat, but use these model capabilities for good. Public models with Mythos’s capability are coming, and we need to be ready.

Why does this even matter?

There's a lot of stories about devices being hacked. Hacking is not hard at all - it takes a bit of knowledge, perserverance and some creativity. It is by no means hard. I'd make a bet that the majority of "smart home" devices could be breached with very minimal effort. It's just that no one has invested the time or effort to do so, due to lack of ROI. The knowledge on common security flaws (think buffer overflows, overly excessive permissions, default credentials) is very widely available and will be baked into these models. As an example that many reading this will have in their homes - this potentially allows these low-skill attacks to be ran against a massive set of targets, from well known robot hoobers to no-name knockoff smart bulbs. Expand that scope to every connected device.

What this means for security

I used to be that little script kid sitting running simulated hacking attempts against other resources on the public Internet. Even I was able to find some vulnerabilities or issues in software systems as a young, inexperienced novice. What we’ve got today is a model that exists in the world that is that script kid, except it’s not a novice, with access to an unending, unfathomable number of resources and information. This is not to say that we should be stopping development of or access to these models; they are an incredibly important capability. What they can do from an educational and even practical standpoint is amazing.

Distribution

This also exacerbates and exposes a wider issue that is already very well documented in device security and software, and it’s the fact that distribution is a problem for updates. Users do not update their software as often as they should, nor do they know they should. Some products do not even have update mechanisms readily available, may not be Internet-connected, or may be connected only by manual intervention, and they are the really, really, really concerning part.

How do we solve this as an industry? I don’t know. It could take many forms.
There could be pressure put on device manufacturers to put engineering effort into securing their lineup. This is expensive, politically challenging, but not impossible. It would prove difficult for companies which are shut down, who have been bought, changed direction, or altered in their operating nature. The other approach could be a community-driven effort in order to proactively build fix and update devices; however, doing that successfully, and it not just being known about to developers, would be a very tricky thing to get right - plus you'd be relying on co-operation with the manufacturers to release the source code, update mechanisms etc.

Going forward

I believe in this regard we need to fight fire with fire. We need to have adversarial models that can run against our applications and act as what are currently our blue teams and red teams, etc., because our security adversaries are using these and we need to start using them to defend our applications and systems. That responsibility does not lie with a company’s cyber security operations centre, nor does it lie with a platform team. It lies with each and every individual developer to push for and advocate for secure systems.
