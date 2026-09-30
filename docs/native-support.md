# Native support

HGL owns node guards, scheduling, state, publication and graph composition.
A native helper supplies the operation named by its HGL contract.

The shared scalar declarations are in `hgl/hgraph/native/scalar_values.hgl`,
`scalar_values_i64.hgl`, `scalar_operators.hgl` and `temporal_values.hgl`. Each selected implementation
must match parameter names, types, constness, result and error policy. Runtime
services are requested by the selected part, not added to the public signature.

The imported `hgraph.native` module also supplies endpoint queries (`valid`,
`all_valid`, `modified`, `last_modified`, `bound`, `active`), collection length
and emptiness, dictionary membership and window queries. Their current C++
view adapters remain in hgraph. They are legacy target interfaces: a portable
raw-time-series helper signature is not settled, so this package does not
invent one or relabel those helpers as scalar functions.

A build supplies one complete `hgraph.native` module and selects its own
implementation parts. hgraph's C++ view adapter modules retain their current
contracts and tests; no embedded C++ body is part of this package. Shared value
helpers are ordinary native declarations. See the
[native contract](https://github.com/hhenson/hgraph_spec/blob/main/language/docs/design/native-implementation-parts.md).

Operator implementations require the scalar helpers they call, for example
`requires native::add(L, R) -> O`. The native value overload determines the
supported domain; the temporal `add_` operator is not its own prerequisite.
Native scalar operator tests live in `tests/native_scalar_operators.hgl`.

The library's HGL tests state observable values, absence of ticks, admission
and error behaviour. The audit repository records cross-runtime results;
generated C++ or Rust is compiler validation output, never a library source.

Scalar `replay` and `record` use the typed `replay_input` and `capture`
capabilities specified by HGL ADR 0016. Eval binds their per-run buffers;
the HGL bodies own replay scheduling/cursor advancement and recorder startup
and capture calls. Providers supply storage access only. The generic
`pass_through` compute and recorder read explicit `delta_value` metadata.
This profile admits bool, i64, f64, str, date, time, datetime and duration.

Known inherited gap: `filter_` may fail to resynchronize after its condition
becomes invalid and reopens. The extracted body is unchanged; the
[follow-up](https://github.com/hhenson/hgraph_std/issues/2) requires a reasoned
invalidation scenario and independent Python/C++ comparison before correction.
