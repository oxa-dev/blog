---
title: Scientific Documents as First-Class Objects on AT Protocol
subtitle: Publishing research to the Atmosphere with the OXA lexicon
abstract: |
  Scientific publishing today relies on centralized platforms that control access, discovery, and permanence. AT Protocol — the decentralized network behind Bluesky — offers a different model: one where data lives in user-owned repositories, discovery is open to any indexer, and identity is portable. We've taken the first step toward bringing scientific documents onto this network: OXA now defines an AT Protocol lexicon under the `pub.oxa.*` namespace. This post introduces the lexicon, explains the design decisions behind it, and describes where we're headed next.
---

Scientific publishing runs on centralized platforms. You upload a paper, and the platform controls who can find it, how long it stays available, and what you can do with it. Your identity, your content, and your audience all belong to someone else's server.

[AT Protocol](https://atproto.com) — the decentralized network that powers [Bluesky](https://bsky.app) — was built around a different set of assumptions. Data lives in signed, user-owned repositories. Any service can index the network. Identity travels with you. These properties were designed for social interaction, but they map remarkably well onto the problems scientific publishing has been struggling with for decades.

**What if a scientific document were just another record on the Atmosphere?**

The easy answer would be to upload PDFs as blobs and call it done. But that misses the point. A PDF on a decentralized network is still a PDF — opaque, monolithic, and inert. The interesting possibilities emerge when we represent the _content itself_ in standardized, structured ways. Structured content enables deep links into specific sections, figures, or claims. It enables [modularity and composability](https://articles.continuousfoundation.org/articles/how-modularity-changes-systems) — individual components of a publication that can be referenced, reused, and recombined independently. And it enables distributed annotations — third parties adding new layers of meaning on top of the original text, without the author or the hosting platform needing to do anything. These are the capabilities that make decentralized scientific publishing worth pursuing, and they all require structure.

We've taken a first step. The OXA [AT Protocol lexicon](https://oxa.dev/articles/documentation/atproto-lexicon) defines the schema for representing scientific documents as structured records in any [Personal Data Server (PDS)](https://atproto.com/guides/the-at-stack) — discoverable on the network's [firehose](https://atproto.com/specs/sync), portable between hosts, and subject to the same sync, auth, and moderation primitives as a Bluesky post. There's still a lot of work ahead, but the foundation is in place.

## Why AT Protocol for science?

The properties that make AT Protocol compelling for social networking are the same properties the research community has been asking for:

- **User-owned data.** Documents would live in the author's signed repository and could be migrated between hosts without losing history or identity.
- **Decentralized discovery.** Any indexer can crawl the firehose to discover and aggregate scientific content — no single platform gatekeeps visibility.
- **Interoperability by default.** The [Lexicon](https://atproto.com/guides/lexicon) type system gives every consumer the same schema, so tools can read, validate, and render documents without out-of-band agreements.
- **Built-in identity.** Authors are identified by [DIDs](https://atproto.com/specs/did) and [handles](https://atproto.com/specs/handle), providing a ready-made, portable identity layer — no need to build yet another login system.

## The `pub.oxa.*` lexicon

The OXA lexicon is organized into three files under the `pub.oxa` namespace:

| NSID                        | Purpose                                                                            |
| --------------------------- | ---------------------------------------------------------------------------------- |
| `pub.oxa.document.document` | The `Document` record type — the root object stored in a PDS                       |
| `pub.oxa.document.defs`     | Block-level definitions (`paragraph`, `heading`, `richText`) and the `block` union |
| `pub.oxa.richtext.facet`    | Facet annotations for inline formatting (`emphasis`, `strong`, `byteSlice`)        |

A `Document` record contains an array of blocks. Each block carries a `text` string and an optional `facets` array that annotates ranges of that text with formatting. This flat structure follows the same pattern AT Protocol uses everywhere. It is one of the most interesting parts of the design.

## From trees to facets

The biggest transformation between an OXA document and its lexicon representation is how inline content works.

In the OXA schema, inline content is a recursive tree. A paragraph has children that can be text, emphasis, strong, or other formatting nodes, and those nodes themselves contain children:

```yaml
type: Paragraph
children:
  - type: Text
    value: 'This is '
  - type: Strong
    children:
      - type: Text
        value: 'bold and '
      - type: Emphasis
        children:
          - type: Text
            value: 'italic'
  - type: Text
    value: ' text.'
```

AT Protocol uses [facets instead of a tree](https://www.pfrazee.com/blog/why-facets). The text is stored as a single plain string, and formatting is described by byte-range annotations:

```json
{
  "$type": "pub.oxa.document.defs#paragraph",
  "text": "This is bold and italic text.",
  "facets": [
    {
      "index": { "byteStart": 8, "byteEnd": 23 },
      "features": [{ "$type": "pub.oxa.richtext.facet#strong" }]
    },
    {
      "index": { "byteStart": 17, "byteEnd": 23 },
      "features": [{ "$type": "pub.oxa.richtext.facet#emphasis" }]
    }
  ]
}
```

The conversion walks the inline tree depth-first, concatenating text node values into a single string and recording byte offsets for each formatting node. This flat representation has real advantages in a decentralized setting: a consumer that doesn't recognize a facet type can still display the plain text, validation is straightforward, and new facet features can be added to the open union without changing the text representation.

## Design decisions

A few choices are worth calling out:

**OXA defines its own facet lexicon** rather than reusing Bluesky's `app.bsky.richtext.facet`. Bluesky's facets are designed for social microblogging — mentions, links, and hashtags. Scientific documents need typographic formatting: emphasis, strong, and in the future subscript, superscript, inline math, and citations. The `app.bsky` feature union is also closed, while OXA's is declared with `"closed": false` for extensibility.

**Compatible features from other namespaces.** Where an OXA facet feature is semantically equivalent to one in another AT Protocol namespace, the converter emits both features in the same facet. A Bluesky client rendering an OXA document will make links clickable even if it doesn't understand `pub.oxa.richtext.facet#emphasis`.

**Generated from the OXA schema.** The lexicon files are generated from the OXA YAML schema definitions. When a new type is added to the schema — a new inline type like `Subscript`, `Cite` or a new block type like `CodeBlock` — it appears in the lexicon automatically after running codegen. No manual lexicon authoring required.

## A growing publishing ecosystem

OXA isn't arriving on AT Protocol alone. A growing ecosystem of tools is already exploring long-form publishing on the network, and that context shapes how we think about the lexicon.

[Standard.site](https://standard.site) defines shared lexicons for publishing metadata — what a publication is (`site.standard.publication`), what a document contains (`site.standard.document`), and how users subscribe to publications (`site.standard.graph.subscription`). Platforms like [Leaflet](https://about.leaflet.pub/), [WhiteWind](https://whtwnd.com), and others already implement these standards, creating an emerging layer of interoperable, decentralized publishing infrastructure.

The OXA lexicon is complementary. Where `standard.site` describes _where_ content lives and _how_ it's discovered, the `pub.oxa.*` namespace describes _what scientific content looks like_ at the structural level — its blocks, inline formatting, and semantic types. A scientific document could use `standard.site`'s publication and document lexicons for discovery and metadata while using the OXA lexicon for its rich content structure. These layers compose naturally on AT Protocol because records can reference multiple lexicons, and consumers ignore types they don't recognize.

This is one of the things that makes AT Protocol's approach compelling: nobody needs to agree on a single monolithic schema. WhiteWind can store blog posts in its own format, Leaflet can store theirs, and OXA can store scientific documents — all discoverable through the same firehose, all portable between PDS providers, and all able to share infrastructure like `standard.site`'s publication metadata where it makes sense.

We're actively exploring how OXA documents can participate in this ecosystem, and we'd welcome collaboration with the teams building these tools.

## Try it now

The `oxa` CLI can convert any OXA document to its AT Protocol representation:

```bash
oxa convert --to atproto document.json
```

Or from YAML via stdin:

```bash
cat document.yaml | oxa convert --to atproto --yaml -
```

The conversion functions are also available programmatically from `@oxa/core`:

```typescript
import { oxaToAtproto } from '@oxa/core';

const record = oxaToAtproto(document, {
  createdAt: new Date().toISOString(),
});
```

See the [full documentation](https://oxa.dev/articles/documentation/atproto-lexicon) for details.

## Distributed annotations: facets as standalone contributions

The facet model opens up a possibility that we think is genuinely exciting for **distributed annotations** on top of the scientific literature.

Because facets are byte-range annotations layered _on top of_ plain text, there is nothing stopping someone other than the original author from publishing new facets that target an existing document. A third party — a reviewer, an institution, an AI agent — could publish a record that attaches additional facet annotations to a paragraph in someone else's document. These annotations live in the contributor's own PDS, signed with their own identity, and can be aggregated by any indexer that knows how to look for them.

Consider a concrete example. A preprint mentions an antibody by name in its methods section, but doesn't include a [Research Resource Identifier (RRID)](https://scicrunch.org/resources). Today, services like [SciScore](https://sciscore.com) already do this kind of annotation — scanning preprints on bioRxiv, identifying reagents, and matching them to RRIDs. But the results are centralized. SciScore generates structured resource tables that bioRxiv displays in its own interface. If you read the same paper somewhere else, the annotations don't follow. And it's ultimately up to bioRxiv to decide whether and how to surface them.

On AT Protocol, this annotation could instead be a facet: a byte-range pointing to the antibody name in the original paragraph, with a feature type like `pub.oxa.richtext.facet#rrid` carrying the identifier. The annotation lives in the annotator's repository, references the original document's AT URI, and is discoverable on the firehose. Any reader, any rendering tool, any indexer can pick it up and overlay it on the original text. No single platform has to opt in.

The same pattern applies to richer semantic annotations. RFC0005 defines citation nodes with an `intent` field drawn from the [Citation Typing Ontology (CiTO)](https://sparontologies.github.io/cito/current/cito.html) — vocabulary for expressing _how_ a citation relates to the cited work (`extends`, `usesMethodIn`, `disputes`, `agreesWith`). CiTO has been around for over a decade, and efforts to get authors to add these annotations at writing time have struggled with adoption; the burden is just too high for most authors [@10.1186/s13321-023-00684-1]. But if citation intent annotations can be contributed _after publication_, by anyone, the calculus changes. A domain expert could annotate the citation relationships in a key paper. An AI agent could propose CiTO intents across an entire corpus. These contributions would be independently attributable, independently verifiable, and composable into overlay views on top of the original.

This is the deeper promise of facets on a decentralized network: **publications become living objects that can be enriched over time by a distributed community, without requiring the original authors or the hosting platform to do anything.** The original text stays immutable in the author's or preprint-servers PDS. Annotations accumulate as separate, signed records from other identities. Consumers choose which annotation layers to display. It's a model that could make post-publication enrichment — RRIDs, CiTO intents, corrections, links to reproducibility artifacts, lay summaries — a natural part of how scientific documents live on the network, rather than something bolted on and owned by a single platform.

## What's next

The lexicon defines the schema, but there's more work to do before scientific documents are fully at home on the Atmosphere.

**Blobs.** Scientific documents contain images, figures, and other binary assets. AT Protocol handles these through [blobs](https://atproto.com/specs/data-model#blob-type) — binary objects uploaded to a PDS and referenced from records. We haven't yet implemented blob support in the converter, so currently only the text and structure of a document make it into the record. Adding blob handling for images and figures is the next major step.

**More block and inline types.** We are actively building out OXA through an RFC process to represent scientific documents; as the OXA schema grows to include code blocks, math, tables, and other content types, the lexicon grows with it automatically.

**Publishing and discovery.** With the schema in place, the next question is how scientific documents get discovered and surfaced. AT Protocol's firehose and feed architecture open up possibilities — scientific feeds filtered by topic, institution, or methodology; building that infrastructure is exciting future work.

**End-to-end workflow.** Today you can convert an OXA document to its AT Protocol representation. The path from there to a record in a PDS, with proper identity and blob handling, is what we are currently working toward.

## Where this is heading

The long-term picture is one where scientific documents are genuine first-class objects on the Atmosphere:

- **Any indexer can build a scientific feed.** The same firehose that powers Bluesky feeds could surface new research.
- **Documents are portable.** Switch PDS providers without losing your publication history or breaking links.
- **Social and scientific infrastructure converge.** Comments, reviews, and discussions happen alongside the content they reference, using the same moderation and identity infrastructure.

We're not there yet, but defining the lexicon is the necessary first step.

## Get involved

The lexicon, converter, and schema are all open source. We are early in this journey and would love your feedback:

- Read the [full AT Protocol lexicon documentation](https://oxa.dev/articles/documentation/atproto-lexicon)
- Browse the [OXA GitHub](https://github.com/oxa-dev)
- Join the conversation on [Discord](https://discord.oxa.dev)

If you're building tools for scientific content on AT Protocol, or thinking about what decentralized publishing could look like, come talk to us.
