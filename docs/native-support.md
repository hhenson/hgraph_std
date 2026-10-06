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

The scalar `midnight(date) -> datetime` helper follows the
[UTC date-conversion contract](date-conversion.md).

The library's HGL tests state observable values, absence of ticks, admission
and error behaviour. The audit repository records cross-runtime results;
generated C++ or Rust is compiler validation output, never a library source.

`replay` and `record` use ordinary `list<TimedValue<T>>` storage.
`replay` receives a const list and yields each absolute timestamp and delta
payload from a generator body. It visits entries in supplied order; silence
is the absence of an entry. `record` receives a temporal input and a const
key, initializes a typed empty global-state list at start, and pushes an
independently retained `TimedValue<T>` on each input publication. It uses
`clock.evaluation_time` and `delta_value(ts)`; completed pushes need no stop
flush. Ordinary typed entry preparation and borrowing follow ADR 0016.

These are independently callable HGL operators. Eval constructs timed lists
from present dense slots, supplies recording keys, and retains recordings
after stop. The original dense lengths belong to eval, so an empty timed list
does not erase trailing silence or invent a replay horizon. Providers supply
ordinary storage, exact typed retention and generator scheduling; there is no
operator-specific input or recording buffer capability. The generic
`pass_through` compute continues to publish explicit `delta_value` metadata.
The profile includes scalar values and recursively sparse sets, fixed lists,
tuples, required-field nominal structs, and maps with i64 keys.
See [replay and recording](replay-record.md) for caller and ownership contracts.

Known inherited gap: `filter_` may fail to resynchronize after its condition
becomes invalid and reopens. The extracted body is unchanged; the
[follow-up](https://github.com/hhenson/hgraph_std/issues/2) requires a reasoned
invalidation scenario and independent Python/C++ comparison before correction.
