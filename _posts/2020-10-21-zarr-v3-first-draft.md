---
layout: post
title:  "Zarr V3 Spect First Draft Publication"
date:   2020-10-21
categories: zarr specs
---

Zarr is a format for the storage of chunked, compressed, N-dimensional arrays,
started in 2018, initially designed for a local file system it has grown through
the years to work on many storage infrastructure, in particular cloud storage
like S3, google cloud storage and many others. The current specification of Zarr
is robust and in production in many existing libraries, but there are a [number
of reasons the pushed us to designed a new version of the
specification](https://zarr-developers.github.io/zarr/specs/2019/06/19/zarr-v3-update.html),
among which a better design for cloud storage, extensibility and being language
agnostic.

After many month of work we are publishing the draft of the [Zarr V3
spec](https://zarr-specs.readthedocs.io/en/core-protocol-v3.0-dev/protocol/core/v3.0.html),
and ask feedback on a few specific areas of interest for the specification. 

We encourage you to read about the high level changes to the specifications as
to whether this would affect your workflow in a positive or negative manner and
send us your feedback by openeing or commenting on a issues on teh [Zarr Spec
Repository](https://github.com/zarr-developers/zarr-specs). 

You can also follow the current draft implementation, but be careful those might
be incomplete and the spec is not final, so use with care. 
  - CZarr/Xtensor LINK (implementation of zarr in C/C++)
  - Zarrita
  - Pull-Request to make the main zarr-python compatible with v3. 

# Main changes to the spec with respect to v2

Unlike Zarr spec v2, the spec v3 has mainly the following differences:

  - Zarr V3 is a flat key-value store instead of a hierarchical store. Hierarchy
    is implied. This make it easier to do concurrent creation of arrays in deep
    hierarchy while avoiding the necessary and often costly step of creating
    intermediary groups. 

  - Zarr V3 has an explicit root, while v2 roots and groups could not be
    distinguished. This mean that you can only open a zarr v3 at a specific
    point, then drill down. 

  - Separation of the data and  metadata key space. This should make it easier
    or unnecessary to separate the metadata from the chunk store in the long run
    by abstracting this at the sore lvel. 

  - Explicit support for extensions. As Zarr-V3 attempt to be language agnostic;
    support for many arbitrary data types has been removed from the core spec
    and delegated to extensions.

  - Chunk separator is ``/`` by default. This should lead to a better
    compatibility with N5 for which this is already the case, and should make
    some filesystem storage more efficient by decreasing the number of inodes
    per directories. 

  - `".json"` suffix for the metadata document by default, and should ease
    exploration with external tools.

# Area we request feedback on. 


We solicit feedback on the following area during the RFC period of this first
draft. 

In zarr v2, nodes names (equivalent to folder and filenames for those of you
with a filesystem mental model) are ascii only and case insensitive. This can be
problmatic for some datasets as case is meaningful. Even when non meaningful, it
can help with human comprehension or reading. In zarr v3 we want to make node
names case sensitive. This might make store implementation more complex, in
particular when the underlying storage backend is not, and we know that some
current os (macOS) are case insensitive. IF you have strong belives or use case
to make zarr case sensitive or not, we welcome your input in [issue 57](https://github.com/zarr-developers/zarr-specs/issues/57)

In the same area, we are wondering whether zarr should support full unicode. It
has many of the same issues as above, but with extra complexity as handling of
Unicode, and casing is much more complicated, and might not be possible on al
platform. So far we've restrained to ascii and fixed-length encoding of nodes
names, but if you have a compelling use case – dataset using international names
– or could make use of it we welcome your input on [issue
56](https://github.com/zarr-developers/zarr-specs/issues/56), same if you have
strong preference for not having support for full Unicode support 


More and more, dataset are now having named dimensions; there are many models to
name dimensions and we are pondering whether to make dimensions part of the core
spec. You can come discus this as part of [issue
73](https://github.com/zarr-developers/zarr-specs/issues/73), and convince us to
promote named dimensions from the status of being and extension to being in the
core. 


# Next step.

While we collect feedback on this first draft of the zarr specification we wil
be working on the implementation. WE welcome you testing, reviewing and
hammering on those. We also expect a number of refactor of the zarr-python
master branch to make integration of the v3 work easier. even if you are not
working with v3 we would highly appreciate you working using the master branch
of zarr and reporting any error or warning you are getting in order to help us
cleaning up the codebase.






