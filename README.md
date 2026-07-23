# rusty_h2

A from-scratch HTTP/2 implementation in Rust, built directly from
[RFC 9113](https://www.rfc-editor.org/rfc/rfc9113) (HTTP/2) and
[RFC 7541](https://www.rfc-editor.org/rfc/rfc7541) (HPACK).

## What's here

This crate currently implements the wire-format building blocks for
HTTP/2. There is no connection driver or async I/O integration yet — it's
the codec layer those would sit on top of.

- **`frame`** — encode/decode for the 9-octet frame header and all ten
  standard frame types: `DATA`, `HEADERS`, `PRIORITY`, `RST_STREAM`,
  `SETTINGS`, `PUSH_PROMISE`, `PING`, `GOAWAY`, `WINDOW_UPDATE`, and
  `CONTINUATION`. Unknown frame types round-trip as opaque data rather
  than erroring, per RFC 9113 §4.1.
- **`hpack`** — a complete HPACK implementation: the 61-entry static
  table, a full Huffman codec (RFC 7541 Appendix B), integer/string
  primitives, an eviction-aware dynamic table, and an `Encoder`/`Decoder`
  pair supporting indexed fields, literal fields (with/without/never
  indexing), and dynamic table size updates.
- **`stream`** — the per-stream state machine from RFC 9113 §5.1
  (`idle` → `open`/`reserved` → `half-closed` → `closed`).
- **`error`** — shared error types distinguishing connection-level errors
  from stream-level errors, per RFC 9113 §5.4.

`CONNECTION_PREFACE` (the 24-octet client preface from RFC 9113 §3.4) is
exported from the crate root.

## Testing

```
cargo test
cargo clippy --all-targets
```

The HPACK test suite includes the literal and Huffman-coded request
sequences straight from RFC 7541 Appendix C.3/C.4, decoded through a
single `Decoder` so the dynamic table evolves exactly as the RFC
describes.

## Roadmap

Not yet implemented: the connection-level state machine (settings
negotiation, flow control windows, GOAWAY handling), an async I/O driver,
and a client/server API surface.
