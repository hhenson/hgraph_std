# Structural consumers of held references

[This suite](../hgl/hgraph/tests/reference_structural_values.hgl) obtains every
REF through ordinary graph bindings. A generic opaque relay and a REF output
with a scalar Boolean latch retain the first designation. Ordinary consumers
bind to that output and emit explicit `delta_value` publications; no REF value
is supplied to eval, replay or record. Reference routing follows TS-16/17 and
the node access rules in the specification.

| Exact consumer shape | Required observation |
|---|---|
| Set and map | Original membership/upsert/removal deltas; no held-value replay |
| Fixed and growing list | Sparse indices, equal child ticks and canonical tail removal |
| Tuple and concrete struct | Original omissions and partial child validity |
| Atomic struct and map of ordinary lists | Complete replacements and independent retained children |
| Rolling scalar and complete struct | Arrival payloads, including equal arrivals before minimum readiness |
| Enum and atomic abstract family | Exact enum identity and concrete family tags |
| Routed map with first designation held | Later route changes never select another endpoint; an invalid target waits for its own first value |
| Ordinary structural child observer | Validity guards suppress unset child reads; present zero publishes |
| Retained ordinary partial struct | Payload arithmetic on its unset field raises existing `value.unset_read` |

The negative case follows the
[unset required-read contract](https://github.com/hhenson/hgraph_spec/blob/main/language/docs/design/unset-required-reads.md):
retaining an unset child is permitted; consuming its payload fails. Direct
invalid temporal reads, missing keys and bounds are separate contracts.

Structural abstract-family roots remain outside admission; the family case
uses exactly `atomic<Family>`. Window payloads are ordinary arrivals, not REF
payloads or copied window state. Lifecycle expiry, graph projections and raw
REF storage keep their existing separate suites and boundaries. Bytes, Any
and native scalar fixtures require their respective frozen dependencies and
remain separate stages.

This corpus has static contract, whitespace and source-delimiter review only.
No HGL backend execution or conformance is claimed.
