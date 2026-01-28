---
title: Introducing the Open Exchange Architecture
abstract: |
  Scientific communication is still largely confined to a paper-shaped box: static PDFs that privilege narrative over utility, while the data, code, protocols, and computation that underpin modern research are pushed into hard-to-access supplements. In this talk, Rowan Cockett introduces the Open Exchange Architecture (OXA), an emerging, community-driven standard designed to break research out of that container. OXA rethinks scientific content as modular, composable components—figures, notebooks, methods, data, and computations—that can be directly referenced, reused, and integrated across the open science ecosystem. Grounded in recent cross-community collaboration and informed by decades of work on authoring, publishing, and reproducible research, OXA aims to shift open science from a focus on access and reading toward one centered on reuse, interoperability, and new modes of discovery—human and machine alike.
---

:::{iframe #oxa-introduction-talk} https://www.loom.com/embed/cdf6c0779f6249fea3f19baabc55bdb9
:placeholder: talk.png
:width: 100%
An introduction to the Open Exchange Architecture, [available online](https://www.loom.com/share/cdf6c0779f6249fea3f19baabc55bdb9).
:::

I am Rowan Cockett from the [Continuous Science Foundation](https://continuousfoundation.org), and today I'm going to be talking about the [Open Exchange Architecture](https://oxa.dev), or OXA, which is developing new standards for modular and composable scientific content.

The challenge that OXA is going after is that science is trapped in a paper-shaped box, or a PDF container, where the content is static, it's narrative-driven. Many of the assets around the research, like the data, code, protocols, notebook, they're relegated to these supplementary zip files that are very hard to gain access to. And then the reviews and credits that are given to the research is on The container itself, rather than attached to the components under it.

:::{figure #fig-trapped-in-a-box} ./trapped-in-a-box.png
Science trapped inside static, narrative PDF containers.
:::

This is happening at the same time as science is really changing how it is created. Data is much large, larger scale than it was previously. And the way that we communicate it, however, has really not kept up to these changes.

> Science has changed. The way we communicate it has not kept up.

The shift that we are seeing at the Continuous Science Foundation is moving from a focus entirely on reading and open science being primarily about access to the content to a future that's more focused on utility and reuse and is designed around these modular components that can be integrated and activated and accessed in a very similar way to, how we think about this today, but also open up new possibilities for modern discovery patterns like through LLMs or ways to integrate computation and data directly into the ways that we communicate our research.

:::{figure #fig-access-to-reuse} ./access-to-reuse.png
Shifting open science from access and reading toward utility and reuse.
:::

This work came out of a meeting in October 2025 From Tools to Adoption [@10.62329/kcep6732] that was funded by the Navigation Fund [@10.71707/GN91-KA32] and co-hosted by [openRxiv](https://openrxiv.org) and the Continuous Science Foundation. Many of the organizations that were at the meeting came together and pooled their **decades of expertise** in dealing with content standards, all the way from, authoring workflows like [R Markdown](https://rmarkdown.rstudio.com/) and [Quarto](https://quarto.org) and [MyST Markdown](https://mystmd.org) and [Curvenote Editor](https://editor.curvenote.com) for what you see is what you get (WYSIWYG) ways to integrate data and computation, and [Stencila](https://stencila.io), who helped [eLife](https://elifesciences.org) with their executable, reproducible articles, into ways that those, pieces of content are able are actually published. And so we had folks like [PLOS](https://plos.org/) in the room and [NeuroLibre](https://neurolibre.org) who are pushing on different ways to to get executable and computational preprints out into the world. These are the folks that are coming together and that helped to found the Open Exchange Architecture project.

:::{figure #fig-from-tools-to-adoption} ./from-tools-to-adoption.png
The “From Tools to Adoption” workshop that catalyzed the Open Exchange Architecture collaboration.
:::

What we were focused on in that room was expanding the focus from solely a narrative-based view of the world into a view where we can share notebooks and code that are integrated into articles, but also make space for these new or even existing artifacts alongside the research.

:::{figure #fig-curvenote-files} ./curvenote-files
Modular research artifacts like notebooks, code, data, and protocols as first-class components.
:::

Instead of taking screen shots, screenshots of large-scale data like this, one and a half terabyte microscopy image, we can actually link and integrate that in that data and interactive visual directly into the ways that we communicate research.

:::{figure #fig-microscopy-zoom} ./microscopy-zoom
Interactive views into large-scale microscopy datasets instead of static screenshots.
:::

Instead of scrolling to a figure or an equation or section using hover references. So that the **content comes to you**. That content should come to you both if you are referencing, in the same article. So you're up high in the article, you don't have to scroll down when you say figure one, you can just look at the content. But also across an open ecosystem of components where you can pull a component from the open science ecosystem. Directly into your research, and those could be interactive backed with computation and live with their attribution and, licensing information as it travels throughout there.

:::{figure #fig-xref-scipy} ./xref-scipy
Hover references that bring figures, equations, and citations to you without scrolling.
:::

That built some of the principles for the Open Exchange Architecture to look towards this future of more modular, interactive, and composable content. Composable components, and some of the requirements for that mean that the actual architecture needs to be open, both in terms of its open source code, but also open ways to join the community and also openly governed through a diverse set of stakeholders. and then the components under underneath there need to both work for today for the types of research artifacts that we're producing, like protocols or preprints or articles or notebooks, as well as be extensible into the future that can be governed, in that evolution through that open community. And because we're thinking a lot about those heavily-typed structures of the data. Those can actually be composable and interoperable with a number of different, systems and formats all the way from authoring workflows, again, like MyST or Stencila or Quarto, to publishing repositories like openRxiv or PLOS. And we're really thinking about exposing the components of existing research and being able to recombine those in the existing new ways, and then thinking about computation and data as core parts of that entire process.

:::{figure #fig-principles} ./principles.png
Principles of the Open Exchange Architecture for modular, interoperable research components.
:::

When we do make modularity possible — not by just dumping Lego out on the floor, this modularity can exist through interfaces like we know today, like preprints or journal articles — we can go **from citing papers** to **granular linking to the citation of the part** that you actually mean. Every node in one of these articles, for example, the figures, tables, equations, outputs, those can be **individually addressable**. Right now, we have reviews, credit, persistent identifiers (PIDs) at the _container_ level and don't have the ability to reach inside.

:::{figure #fig-granular-linking} ./granular-linking.png
Granular linking that lets you cite specific figures, tables, or outputs instead of whole papers.
:::

When we can reach inside, that enables new interfaces, like hover references, that bring the attribution and licensing information with it. You can imagine these new types of interfaces if you've used any AI tooling today. Right now, there's just a very simple link out to a container of research. In the future, those LLM toolings can actually reach into the contents of those documents and show their references directly in line. This is the type of infrastructure that opens up **new tooling and new interfaces and new applications** on top of an open-science corpus.

:::{figure #fig-rfc-process} ./rfc-process.png
The open, community-driven RFC process that governs the Open Exchange Architecture.
:::

This entire process is open and community-driven. There's a [request for comments](https://oxa.dev/rfc) (RFC) process where we are thinking about the technical structure of this and then proposing that getting input from the community. We are **driving the implementation at the same time**. In 2026, we'll be launching some large-scale pilots of the Open Exchange Architecture on existing repositories of research.

When we peer into the future, some of the possibilities that this opens up, this is really about moving towards utility and reuse and thank you thinking about the components of research that, that again, may be embedded in other papers, but we're able to bring those claim level discovery and like change the way that we're searching. We can have method reuse that's actually the methods that can live outside of a research paper or inside of a research paper. We're thinking about versioning so that we can move towards more, you know, continuous and living ways to communicate research and bringing those trust signals to the components of research rather than being stuck on the container level. And all of these things enable us to get towards more composability of knowledge, bringing these building blocks together in new ways, transcluding them from one artifact into the other without losing provenance or attribution or licensing information.

:::{figure #fig-outcomes} ./outcomes.png
Outcomes of OXA: discovery, method reuse, versioning, and composable knowledge workflows.
:::

These are some of the principles and ideas and directions that we're going towards, at the Continuous Science Foundation through the OpenExchange Architecture Project. If you do want to learn more, visit <https://oxa.dev> to join. We also have a [Discord community](https://discord.oxa.dev) where you are having real-time discussions and there's ways to sign up for mailing lists and groups on that as well.

Thank you very much. 🚀

## Acknowledgements

This talk was given at the National Academies of Science Action Collaborative in January 2026 and has had much input and shaping from Nokome Bentley (Stencila), Tracy Teal (openRxiv), and Courtney Babott (Continuous Science Foundation).
