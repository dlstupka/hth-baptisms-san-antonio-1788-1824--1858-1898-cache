# HTH — San Antonio Baptisms 1788–1824, 1858–1898 — Cache

Collection-scoped build and development artifact cache for the **San Antonio
Baptism Records, 1788–1824 and 1858–1898** collection.

This repository is the **non-authoritative, reproducible cache** used by the
[Hidden Texas History (HTH)](https://github.com/dlstupka/hth) framework for
expensive derived artifacts that are specific to this source collection. It is
intentionally separate from the HTH software repository, the archival source
repository, the general model mirror, and the authoritative results repository.

The initial use is persistent learned-detector Golden Set evidence. Neural
inference is independent of calibration parameters, so compatible evidence can
be computed once and reused by later smoke tests, full regressions, capacity
shards, and Golden Set coordinator lanes.

Everything stored here is disposable: deleting the cache may make a build
slower, but it must never change a scientific result or prevent the evidence
from being regenerated from its recorded source, model, and processing
provenance.

## Resolution and recovery flow

When an HTH build requires collection-specific learned evidence, it resolves
the artifact in this order:

1. **Run-local evidence** — use already validated evidence prepared during the
   current build.
2. **Collection cache release** — retrieve the exact immutable release asset
   when its complete identity and checksum match.
3. **Regenerate** — run inference from the authoritative source images and
   verified model, then validate the resulting evidence.
4. **Publish the cache miss** — package the validated evidence and publish a new
   immutable release for compatible future builds.
5. **Fail safely** — if neither retrieval nor regeneration succeeds, fail
   rather than substituting stale or partially compatible evidence.

A cache hit avoids repeated inference only. Detector calibration, quality
measurements, and result authority remain independent of the cache.

## Immutable cache releases

Large cache artifacts are distributed through **GitHub Releases**, not stored
as ordinary Git blobs. Git history should remain limited to documentation and
small machine-readable index metadata when an index is required.

Each release is write-once and content-addressed. A changed input or contract
creates a new identity and release rather than replacing an existing asset. A
learned-evidence identity records at least:

- source collection and immutable source revision;
- Golden Set identity, manifest SHA-256, and ordered page identities;
- detector identity and evidence schema version;
- model identity and verified model SHA-256;
- maximum image dimension and preprocessing contract;
- inference backend and serving-contract identity where relevant;
- evidence representation and page count;
- artifact byte size and SHA-256; and
- creation provenance, including the producing HTH revision.

A release should contain the evidence package, a machine-readable manifest,
and checksums sufficient to validate the package before use. Release tags and
asset names are derived deterministically from the canonical evidence identity.

Cache artifacts must not contain model weights, archival source images,
credentials, runner state, or authoritative calibration/results indexes.

## Repository responsibilities

| Repository | Responsibility |
| --- | --- |
| [HTH](https://github.com/dlstupka/hth) | Software, detector implementations, cache identity contract, workflows, and validation |
| [Source collection](https://github.com/dlstupka/hth-baptisms-san-antonio-1788-1824--1858-1898) | Archival source identity, immutable source releases, and Golden Set releases |
| [Results](https://github.com/dlstupka/hth-baptisms-san-antonio-1788-1824--1858-1898-results) | Authoritative analysis, calibration, runtime intelligence, reports, and lightweight cache references |
| [HTH mirror](https://github.com/dlstupka/hth-mirror) | Non-authoritative redundancy for reusable external model/runtime artifacts |
| **This repository** | Reproducible, collection-specific derived artifact cache |

## Authority and lifecycle

This cache is never a source of research authority. Authoritative provenance
belongs to the source collection, verified model sources, HTH methodology, and
the results repository. Every cache hit must be an exact identity match and
must pass checksum and manifest validation before use.

Entries may be retained while they serve active Golden Sets and detector/model
contracts. Unreferenced entries may be removed to control storage growth. Cache
loss requires regeneration, not recovery of scientific state.
