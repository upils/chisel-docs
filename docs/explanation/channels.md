---
myst:
  html_meta:
    description: "Explanation of channels in Chisel: how tracks and risks select which revision of a store package is fetched and which slice contents apply."
---

(channels_explanation)=

# Channels

Packages fetched from Debian archives always resolve to the latest version
available in the archive. Packages fetched from
{ref}`stores<chisel_yaml_format_spec_stores>`, however, are published per
**channel**, so Chisel needs to know which channel to fetch a package from.

## Anatomy of a channel

A channel is composed of a **track** and a **risk**:

```none
<track>/<risk>
```

- The **track** identifies a line of development of the package, typically
  named after the upstream version it follows, such as `2.0` or `3.0`.
- The **risk** indicates the stability of the published revision. The available
  risks are, from the most to the least stable, `stable`, `candidate`, `beta`
  and `edge`.

Whenever a risk is not given, Chisel uses the `stable` risk implicitly. Thus,
`3.0` and `3.0/stable` refer to the same channel.

A channel may also hold an ephemeral **branch**, as in
`<track>/<risk>/<branch>`. Branches are used to distribute short-lived fixes.

## Choosing a channel

There are two ways a channel is decided:

- Explicitly, by appending it to the slice reference on the command line, as in
   `chisel cut … mybin_myslice@2.0/edge`. See
   {ref}`cut_command_reference_channels`.
- Implicitly, from the
   {ref}`default-track<slice_definitions_format_default_track>` of the package,
   which is mandatory for every package fetched from a store. Note that a slice
   definitions file only declares a track, so the risk stays implicit and
   resolves to `stable`.

Because a package is fetched only once, all the selected slices of the same
package must agree on the channel.

## Channels and slice definitions

The content of a package may differ from one channel to another. To describe
this in a single slice definitions file, _contents_ paths and _essential_ entries
accept a
{ref}`channel<slice_definitions_format_slices_contents_channel>` field holding
the {ref}`patterns<slice_definitions_format_channel_patterns>` of the channels
they apply to. Entries that do not match the channel being cut are silently
skipped, just like entries that do not match the architecture being cut.

The channel is deliberately kept separate from the identity of a slice. It is
not recorded in the {ref}`chisel_manifest_ref`, and it takes part in neither
dependency resolution nor path conflict detection.
