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

_This post was written in response to the Claude Mythos news; however,
its implications reach far beyond Anthropic and apply to much of the
wider software and security industry._

### Claude Mythos

The Mythos model represents a jump in capability that we have not seen
in a while, and I’d recommend reading the system card rather than having
me rehash its claims here. In essence, it is a highly capable model
which, like state-of-the-art (SoTA) models before it, lowers the barrier
to entry for technical tasks. This time, that barrier appears to be the
one surrounding security, penetration testing, and vulnerability
research.

We are getting to the point where, cost and access aside, increasingly
autonomous systems can run meaningful attack workflows against a
particular target, exploring different avenues in parallel and doing so
meticulously and persistently.

There are a few different areas within this news that are worth breaking
down.

### The benchmarks

An important caveat is that, whilst Anthropic have released very
promising benchmarks, this model is only available to private groups at
the time of writing. That means we cannot independently verify those
benchmarks, or the model’s real-world performance more broadly.

Even so, this appears to be a large but natural step forward in
capability, and one that the industry is going to have to come to grips
with.

### The private model

The model being held back is, to me, both understandable and concerning.
Anthropic appear to feel they need to restrict access in line with their
constitution and their stated goal of doing AGI safely, and in principle
that makes sense.

The issue is that AI capabilities tend to proliferate quickly. OpenAI,
Google, and others are usually not far behind one another, and it would
be surprising if similar capabilities were not already being explored
elsewhere. Keeping a model behind closed doors and limiting it to
private groups may reduce short-term risk, but it does little to address
the longer-term concerns now on the table.

As engineers, we need to understand the shape of these capabilities so
that we can adapt and build systems that not only withstand this shift
in the threat landscape, but also make constructive use of the same
capabilities on the defensive side. Public models with Mythos-like
capability are likely coming, and we need to be ready.

### Why does this even matter?

A lot of software and connected devices are already far less secure than
people assume. Many real-world attacks do not require elite expertise;
they rely on common weaknesses, persistence, and a willingness to try
known techniques repeatedly until something works.

I would be willing to bet that the majority of “smart home” devices
could be compromised with relatively little effort. In many cases, it is
not that the attacks are impossible or especially novel; it is that few
people have had the incentive to spend the time doing it at scale. The
knowledge around common security flaws (buffer overflows, excessive
permissions, default credentials, poor authentication flows) is already
widely available, and these are exactly the kinds of patterns that
models can absorb and apply.

Take a category many will have in their homes: robot hoovers, cameras,
smart bulbs, plugs, and other connected devices. AI-assisted attack
workflows lower the cost of trying the same low-skill or moderately
skilled attacks across a huge number of targets. Then expand that idea
to every connected device. In many cases, variation between
manufacturers, firmware, and hardware may have acted as a kind of
accidental protection against large-scale automated exploitation.

### What this means for security

When I was younger, I was exactly the sort of script kiddie running
basic **simulated** attacks against systems. Even as an inexperienced
novice, it was possible to find vulnerabilities or misconfigurations in
poorly defended software systems.

What we have now is not just that same level of curiosity or trial and
error. We now have models that can play a similar role, except they are
not novices: they have access to vast amounts of information, can work
persistently, and can iterate far faster than a person manually trying
things one at a time.

This is not to say that we should stop developing or using these
models. They are an incredibly important capability, and what they can
do from both an educational and practical standpoint is remarkable. But
we do need to be realistic about what they mean for the security
baseline of the systems we build.

### Distribution

This also exacerbates and exposes a wider issue that is already very
well documented in software and device security: distribution and
updates remain a major weak point.

Users do not update their software as often as they should, and often do
not know that they need to. Some products do not even have proper update
mechanisms. Others are not Internet-connected, or are only updated
through manual intervention. Those systems are the really concerning
part.

How do we solve that as an industry? I do not know, and I do not think
there is a single answer.

One option is greater pressure on device manufacturers to invest the
engineering effort needed to secure and maintain their products across
their full supported lifetime. That is expensive and politically
difficult, but not impossible. It becomes especially difficult when
companies shut down, are acquired, change direction, or otherwise stop
supporting products that remain deployed in the world.

Another possibility is some kind of community-driven effort to maintain
or update unsupported devices. In theory, that could help; in practice,
making that work safely, legally, and at scale would be extremely
difficult. It would also rely on cooperation from manufacturers around
source code, documentation, signing keys, update mechanisms, and other
practical constraints.

Either way, we still have to deal with the fact that many devices in our
homes, offices, and cars are insecure. The fact that they have not yet
been breached does not mean they are secure. It may simply mean that no
one has put sustained effort into breaking them yet.

New products absolutely must be designed with this threat model in mind.
We should assume that throughout a product’s lifetime it will face
constant, automated, and increasingly capable attack attempts.

### Going forward

I believe we need to fight fire with fire.

We need defensive and adversarial models that can run against our
applications and systems in the same way that red teams, blue teams, and
security researchers do today. If attackers are using these tools, and
they increasingly will be, then defenders need to use them too.

Take a web system, for example. Create an environment identical to
production, with as much realistic data as possible. Isolate it, and run
a set of agentic models against it with the goal of finding flaws and
holes. Let them operate autonomously within that environment and see how
they perform. Perhaps this could be a part of an applications CI/CD pipeline

That does not mean responsibility sits only with a company’s security
operations centre or a platform team. Security has to be treated as a
shared engineering responsibility. Individual developers, platform
teams, security engineers, and product organisations all need to push
for and advocate for more secure systems.

AI is already a force multiplier for writing software. It now also needs
to become a force multiplier for securing it.
