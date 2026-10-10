# hgraph_std

The public standard library for HGL, written in HGL. It contains operator
contracts, HGL bodies, HGL tests and shared native declarations. It contains
no Python, C++ or Rust implementation and selects no target provider.

- [Standard module](hgl/hgraph/standard.hgl), with control, stream and temporal parts
- [Operator contracts](hgl/hgraph/operators.hgl)
- [Date to datetime conversion](docs/date-conversion.md)
- [Ordinary scalar replay and record](docs/replay-record.md), also configured by `eval`
- [Empty sparse delta validity and repeated-application controls](hgl/hgraph/tests/empty_delta_validity.hgl)
- [Independently sampled endpoint state](docs/state-observation.md)
- [HGL implementations](hgl/hgraph/impl)
- [Behaviour tests](hgl/hgraph/tests)
- [Required payload-read failures and retention controls](hgl/hgraph/tests/unset_required_read_values.hgl)
- [Bytes values, publications and independent retention](hgl/hgraph/tests/bytes_values.hgl)
- [Any boxes, exact payload identity and independent retention](hgl/hgraph/tests/any_values.hgl)
- [Native atomic scalar fixture and shared values tests](docs/native-atomic-values.md)
- [Native support](docs/native-support.md)

The [language specification](https://github.com/hhenson/hgraph_spec) defines
these constructs. [hgraph_spec_audit](https://github.com/hhenson/hgraph_spec_audit)
exercises them against released Python and C++ hgraph.

A compiler build selects this repository at a fixed commit and supplies its
own native implementation. C++ providers live in hgraph; Rust providers live
in the private Rust implementation. No generated native code is checked in here.
Files directly under `hgl/hgraph/` declare operator contracts and properties.
The matching `impl/` parts contain bodies, helpers and instantiations; `tests/`
parts contain behaviour tests. Builds assemble contracts with the chosen HGL
implementation parts, and add test parts only for validation. Native declarations
remain under `native/`; target providers are supplied by the implementation.

The C++ consumer compiles the pinned sources directly and validates their HGL
tests and an installed-SDK consumer.
