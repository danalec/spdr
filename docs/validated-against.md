# Validated against

The enumerable set of real DDR5 modules this decoder is confirmed correct on; every public correctness claim is backed by an entry here, not by a vibe.

## TEAMGROUP T-Create Expert 6000 CL38 · UD5-6000

- **Fixture:** `spdr/tests/fixtures/teamgroup-ud5-6000_0104eef6.spd` (1024 bytes, sha256 `cecfa75eb704272ad5b135e77a534cc416aec55a8daea54823b5dbf6d7761c98`).
- **Module:** TEAMGROUP T-Create Expert 6000 CL38. Part number UD5-6000; full part code CTCED532G6000HC38ADC01. Rated DDR5-6000, 38-38-38-78, 1.25 V.
- **Capacity / organization:** 16 GB, single rank, x8 devices, 16 Gb monolithic dies (consistent with the Phase 1 decode below).
- **Source:** `ubihazard/ddr5-spd-recovery`, file `dumps/teamgroup/t-create-expert_6000_38-38-38-78_1.25_1x8_16x2_[ctced532g6000hc38adc01]/ud5-6000_0104eef6.spd`.
- **Provenance:** public community dump. The SPD content is factual module data, attributed to the source collection. Cross-checked field by field against independent open decoders (`decode-dimms`, pyhwinfo, memtest86plus, edlf `DDR5SPDEditor`) and the part rating; correctness is confirmed at review, not asserted by the snapshot alone.

### Confirmed by Phase 1 (identity and base block)

DDR5 SDRAM, UDIMM (non-hybrid), SPD revision 1.0, SPD device size 1024 bytes; 16 Gb density per die, monolithic (1 die), 16 row / 10 column address bits, x8 I/O width, 8 bank groups x 4 banks per group (32 banks); 1 package rank per channel (symmetric), 2 channels per DIMM, 32-bit primary bus width per channel (2 x 32 = 64-bit, non-ECC).

### Confirmed by Phase 2 (base configuration CRC)

Main configuration CRC `0x8021`: computed over bytes 0-509 with CRC-16/XMODEM and read from the stored bytes 510-511 (little-endian), computed equals stored equals `0x8021`, matching the published reference for serial 0104eef6.

### Confirmed by Phase 3 (base JEDEC timing block)

Base JEDEC speed: DDR5-4800 (tCKAVGmin 416 ps), CL40 implied (tAA 16.640 ns / tCKAVGmin). This is the base fallback the module guarantees; the advertised DDR5-6000 38-38-38-78 lives in the XMP and EXPO profiles and is confirmed in Phase 9, not here.

Decoded base timings: tCKAVGmin 416 ps, tCKAVGmax 1000 ps; supported CAS latencies {22, 24, 26, 28, 30, 32, 34, 36, 38, 40}; tAA / tRCD / tRP 16.640 ns, tRAS 32.000 ns, tRC 48.640 ns (= tRAS + tRP), tWR 30.000 ns; tRFC1 295 ns, tRFC2 160 ns, tRFCsb 130 ns; tRRD_L 5 ns / 8 nCK, tCCD_L 5 ns / 8 nCK, tCCD_L_WR 20 ns / 32 nCK, tCCD_L_WR2 10 ns / 16 nCK, tFAW 13.333 ns / 32 nCK, tWTR_L 10 ns / 16 nCK, tWTR_S 2.5 ns / 4 nCK, tRTP 7.5 ns / 12 nCK. The tRFC values (295 / 160 / 130 ns) are the exact JEDEC 16 Gb figures, corroborating the Phase 1 density of 16 Gb per die.

### Confirmed by Phase 4 (unbuffered module-specific block)

The fixture is a UDIMM, so the module-type dispatch routes it to the unbuffered decode. Decoded unbuffered fields (bytes 230-233, inside the main-CRC-covered range 0-509, so already integrity-checked):

- Module nominal height: 32 mm (byte 230 = 0x11; `(byte & 0x1f) + 15`). This is the top of the 31 < h <= 32 mm range, consistent with a 31.25 mm UDIMM.
- Module maximum thickness: 2 mm front, 1 mm back (byte 231 = 0x01; each nibble + 1 mm).
- Reference raw card: card A, revision 0 (byte 232 = 0x00; code 0 on the JEDEC alphabet `ABCDEFGHJKLMNPRTUVWY`, revision bits [6:5] = 0).
- Rank 1 edge-connector-to-DRAM address mapping: mirrored (byte 233 bit 0 = 1). The full byte (0x81) is preserved raw; only bit 0 is interpreted. Byte 233 is the defined `dimmAttributes` field (per edlf), so its set bit 7 is a bit of undocumented meaning within a defined field, preserved raw and deliberately not treated as a reserved-bit violation (settled in Phase 10), not a reserved-set bit awaiting the linter.

Offsets are pinned against edlf `DDR5SPDEditor` (`ddr5spd_structs.h`) and the UniC `SCA08GU04M1F1C-48B` datasheet block map (bytes 192-239 are common module parameters, Annex A.0); encodings against decode-dimms and JEDEC Standard 21-C Annex K. Correctness of these physical fields is confirmed at review against DDR5SPDEditor's physical-attribute readout and the part's mechanical detail, not by the snapshot alone.

SODIMM, RDIMM, and LRDIMM module-specific decoding is **not yet implemented**. Those types resolve to an explicit not-yet-decoded result that names the type and parses no fields: their substantive content is the per-type register (RCD) and data-buffer block at bytes 240+, which is not guessed without a real fixture. Decoding each is deferred to a later phase gated on a real module of that type.

### Confirmed by Phase 5 (manufacturing block)

The manufacturing block sits at bytes 512-554, past the byte-509 end of the main CRC, so the Phase 2 integrity floor does not reach it. The verification is the published reference for serial 0104eef6 instead. Four fields are self-verifying oracles; the rest are confirmed at review against DDR5SPDEditor's readout.

- Module manufacturer: `0x04ef` -> JEP-106 bank 5, code 0x6f -> "Team Group Inc." (the TEAMGROUP brand). **Oracle match.**
- Manufacturing date: week 37 of 2023 (bytes 515-516 BCD). **Oracle match.**
- Serial number: `0104EEF6` (bytes 517-520). **Oracle match.**
- Part number: "UD5-6000" (bytes 521-550 ASCII, trailing padding trimmed). **Oracle match.**
- Manufacturing location: 0 (byte 514, manufacturer-specific raw code).
- Module revision code: 0 (byte 551).
- DRAM manufacturer: `0x80ad` -> JEP-106 bank 1, code 0x2d -> "SK Hynix" (bytes 552-553).
- DRAM stepping: 255 (`0xff`, the conventional "not specified") (byte 554).

Offsets pinned against edlf `DDR5SPDEditor` (`ddr5spd_structs.h`) and pyhwinfo; the JEP-106 parity/bank convention against decode-dimms; manufacturer names against the freeipmi JEDEC manufacturer ID table. The JEP-106 names "Team Group Inc." and "SK Hynix" are the fixture's two verified entries; the rest of the embedded table is cited reference data.

This **closes the two reference markers carried since Phase 1**: the module manufacturer ID `0x04ef` and the manufacturing date week 37 of 2023 are now decoded and matching the published reference, no longer to-be-confirmed.

### Milestone: base JESD400-5 content decode complete for unbuffered DDR5 modules

With Phase 5, the base JESD400-5 SPD content is fully decoded for an unbuffered (UDIMM) DDR5 module: identity and base geometry, the base configuration CRC, the base JEDEC timings, the unbuffered module-specific block, and the manufacturing information block. The fixture decodes end to end and its published-reference fields (CRC `0x8021`, manufacturer 0x04ef -> TEAMGROUP, serial 0104eef6, part UD5-6000, week 37 / 2023) all reproduce exactly.

The limits of that claim, stated honestly:

- **XMP and EXPO are not JESD400-5.** The advertised DDR5-6000 38-38-38-78 profiles are vendor extensions in the end-user-programmable region, outside the JESD400-5 base content. They are decoded in Phase 9 and are not part of this milestone.
- **SODIMM, RDIMM, and LRDIMM remain deferred.** Their module-specific register and data-buffer blocks are not decoded; those types still resolve to an explicit not-yet-decoded result, each gated on a real fixture in a later phase.

### Linter baseline (Phase 8)

Under the Phase 8 rule set (the single capacity-consistency rule), the fixture produces **zero lint findings**. Its geometry is internally consistent: the primary bus width per channel (32 bits) is a positive integer multiple of the SDRAM I/O width (x8), so the per-rank device count is the whole number 4, and the capacity derives to 16 GB (matching the part rating and the Phase 1 decode). This is the clean-lint baseline the `fixture_lints_clean` test enforces permanently: as rules are added in later phases, a rule that flags this valid module is a bug in the rule, and that test catches it. The capacity formula and its pinned source are recorded in `docs/numerical-claims.md`.

### Confirmed by Phase 9a (XMP 3.0 and EXPO vendor profiles)

The advertised DDR5-6000 profiles live in the end-user-programmable region above the JEDEC base, as Intel XMP 3.0 (magic `0x0C 0x4A` at byte 640) and AMD EXPO (magic `"EXPO"` at byte 832). Both are present and both decode. Because this is the thinnest-referenced region in the project, the decode is anchored by two independent oracles, not trusted on its own.

**Section CRCs (the region anchor).** Each profile section stores a CRC-16/XMODEM, computed here with the same `crc16` primitive verified in Phase 2 and compared to the stored bytes. Computed equals stored for every section:

- XMP 3.0 header: `0x252C`, over bytes 640-701, stored at 702-703.
- XMP 3.0 profile 1: `0x0A5F`, over bytes 704-765, stored at 766-767.
- XMP 3.0 profile 2: `0x0AC4`, over bytes 768-829, stored at 830-831.
- AMD EXPO block: `0x9FE2`, over bytes 832-957 (one CRC for the whole block), stored at 958-959.

The match over each pinned range confirms the region, the range, and that the algorithm is the same CRC-16/XMODEM as the base block. The dump source published the presence of these sections, not the specific hex values, so the computed-equals-stored match plus the rated-timing oracle is the confirmation.

**Rated timings (the value oracle).** The decoded XMP profile 1 and the decoded EXPO profile 1 each reproduce the part's box rating, cross-checking the same numbers two independent ways: DDR5-6000 (tCK 333 ps), CL38 (tAA 12654 ps / tCK = 38), tRCD / tRP 12654 ps each (38 clocks), tRAS 25974 ps (78 clocks), and VDD / VDDQ 1.250 V with VPP 1.800 V (voltage byte `0x25` / `0x30` under `(byte>>5)*1000 + (byte&0x1f)*50` mV). The two formats decode to identical rated values. The named profiles are "TG-6000-38-38-78" (profile 1) and "TG-5600-40-40-84" (profile 2, DDR5-5600 40-40-84 at 1.20 V), both decoded.

Offsets are pinned against memtest86plus `system/spd.c` (the XMP magic and per-profile timing offsets) and edlf `DDR5SPDEditor` (`ddr5spd_structs.h` for the header / profile / EXPO field order; `utilities.cpp` for the voltage encoding and the CRC-16 parameters), with each CRC range confirmed by computed equals stored. The decoded-versus-preserved-raw-versus-deferred boundary is recorded in `docs/numerical-claims.md`: decoded are the four CRCs, the cycle time / data rate, CAS, tRCD, tRP, tRAS, the voltages, and the XMP names; deferred (inside the CRC-confirmed region but not surfaced) are the remaining profile timings, the XMP `vMemCtrl` and metadata, and the EXPO per-profile enable-bit semantics.

This **closes the last reference markers carried for the fixture**: the Phase 1 rated-speed marker (DDR5-6000 38-38-38-78 at 1.25 V, now decoded and verified, not just the part rating) and the Phase 2 XMP and EXPO section-CRC markers (now computed-equals-stored for all four sections).

### Linter, expanded rule set (Phase 9b)

Under the Phase 9b rule set (timing-relationship, clock-consistency, and speed-bin rules added to the Phase 8 capacity rule), the fixture still produces **zero lint findings**, with the base block and both vendor profiles passing every applicable rule. The relationships it satisfies:

- **Base block:** tRC = tRAS + tRP (48640 = 32000 + 16640 ps); tRAS >= tRCD (32000 >= 16640); tRC >= tRAS (48640 >= 32000); the operating CL = tAA / tCK = 16640 / 416 = 40 is an integer and is in the decoded supported-CAS set {22, 24, ..., 40}; tRCD and tRP are whole multiples of tCK; the base rate 4800 MT/s is a JEDEC-standard bin.
- **Vendor profiles** (the applicable subset, since a profile carries no tRC and no supported-CAS set): the DDR5-6000 profile has tRAS 25974 >= tRCD 12654, an integer CL of 38 (12654 / 333), tRCD/tRP whole multiples of tCK, and a standard 6000 rate; the DDR5-5600 profile has tRAS 29988 >= tRCD 14280, an integer CL of 40, and a standard 5600 rate.

This extends the `fixture_lints_clean` invariant to the new rules: every rule emits only on a genuine violation, and no rule flags a vendor profile for being tighter or faster than a JEDEC bin (the recognized-rate check is Info-only, and no rule compares a profile to JEDEC limits). The full JEDEC sub-grade-table conformance and the tFAW >= 4 x tRRD_S ordering (tRRD_S is not decoded) are deferred, recorded in `docs/numerical-claims.md`. The relationships, the standard-rate list, and their sources are pinned in `docs/numerical-claims.md`.

### Linter, full rule set (Phase 10)

Under the full rule set (Phase 8 capacity, Phase 9b timing and speed-bin, and the Phase 10 reserved-bit and consistency rules), the fixture still produces **zero lint findings**, with every applicable rule running.

- **Reserved-bit rule:** the fixture has zero in every region the rule checks, pinned to edlf `ddr5spd_structs.h`'s named `reserved_*` members and confirmed zero on the fixture: bytes 15, 29, 103-127, 128-191, 214-229, 236-239, and 448-509. Two reference-declared-reserved regions are deliberately excluded, never flagged: `reserved_240_447` (the module-type-specific parameter region, which a valid RDIMM/LRDIMM populates) and `reserved_555_639` (non-zero in the fixture itself at bytes 576-581, so vendor-usable). The rule checks only regions reserved for every module type and zero on the valid module.
- **Consistency rule:** the fixture's geometry is coherent, a monolithic package carrying exactly one die, so the package/die-count rule emits nothing.

**Deliberately not flagged, with the reasoning on the record.** Two bits the valid fixture sets are not lint findings, because a valid module setting them is positive evidence they are not defects:

- **Byte 233 bit 7** (byte 233 = `0x81`): edlf labels byte 233 a defined `dimmAttributes` field, not a reserved region, and the paywalled spec does not let us confirm bit 7 is reserved. A valid module setting it is evidence it is defined-but-undocumented, not a reserved-must-be-zero violation. It is not in the reserved map, locked in by the `byte_233_bit_7_is_not_a_reserved_finding` test.
- **The rank-1 address-mirror bit** (byte 233 bit 0): a single-rank module has no second rank to mirror, so the set bit is a benign don't-care, not an inconsistency. No rule inspects it.

Both remain visible in the decode output as `module_attributes_raw = 0x81` (Phase 4), preserved-raw rather than guessed; this phase simply does not elevate them to findings. The reserved map, the coherence relationship, and their sources are recorded in `docs/numerical-claims.md`.

This completes the linter's four rule categories (capacity, timing/speed-bin, reserved-bit, cross-field consistency) with the fixture-lints-clean invariant held at zero throughout.

### Linter CLI (Phase 11)

The fixture also lints clean through the CLI: `spdr lint` over it produces no findings and exits 0 (the `--json` form is the empty array `[]`). This is the same zero-findings baseline as the core `fixture_lints_clean` test, now exercised through the user-facing surface and its exit-code contract (0 clean or info-only, 1 a warning or error finding, 2 operational). No new correctness claim: Phase 11 is surface work over the unchanged lint core.

### v0.1.0 release (Phase 12)

The v0.1.0 release packages the decoder and the four-family linter for crates.io
with no new decode, rule, fixture, or correctness claim. The validated set is
unchanged and is exactly the one module above, the TEAMGROUP UD5-6000: unbuffered
(UDIMM) content is complete, and SODIMM / RDIMM / LRDIMM module-specific decoding,
full JEDEC bin-table conformance, and the tFAW >= 4 x tRRD_S ordering remain
deferred and gated on real fixtures. The no-panic contract is property-tested; the
cargo-fuzz harness is committed but not deep-run, so this is "property-tested," not
"fuzzed." See `CHANGELOG.md` for the release summary.

## Public sample set (TechPowerUp SPD-Z), six DDR5 modules

Six additional real modules, added together. They widen the enumerable set from one module to seven and, more usefully, they are the first coverage of four things the original fixture could not speak to at all: a **second EXPO profile**, a module whose **EXPO tRAS is below its tRCD**, a **CUDIMM**, and a module with **no XMP block at all**.

### Provenance is weaker than the entry above, and here is exactly how

These six come from the TechPowerUp SPD-Z sample collection, not from a community SPD-recovery repository. That difference matters to every claim below and is stated before the claims rather than after them.

- **The dumps are partially anonymised.** Five carry serial `99999999` and a placeholder part number (`REPLACE TEXT WITH SKU` or `REPLACE WITH SKU`); one carries a genuine serial, `0202CBF7`, with the part text replaced. So the **JEP-106 manufacturer oracle that the entry above uses is unavailable here** — the module-manufacturer field decodes to a raw bank/code pair rather than a name, and `module_manufacturer_id` is not surfaced for these. That is not a decoder defect; the field is genuinely scrubbed at source.
- **What survives is stronger than a name check, and is what these are actually validated on.** Every dump's stored base configuration CRC recomputes exactly (the table below), which is a content-integrity proof over bytes 0-509 independent of any label. Every vendor block CRC recomputes exactly. And on the four modules carrying both XMP 3.0 and AMD EXPO, the two independent encodings of the same advertised rating **decode to identical values** — a genuine cross-format agreement that no single-format dump can provide.
- **No field-by-field cross-check against other decoders was performed for these six.** The entry above carries that; these do not, and claiming it would be claiming work not done.

### The set

| Fixture | sha256 (first 16) | Base CRC | Module | Profiles |
| --- | --- | --- | --- | --- |
| `biwin-ddr5-6000_99999999.spd` | `00014112383ef10c` | `0xC567` match | UDIMM, 16 Gb, x8 | EXPO + XMP, both CL28 |
| `gskill-ddr5-6000-expo_99999999.spd` | `7f9e6a024f6e7d6b` | `0x89D8` match | UDIMM, 16 Gb, x8 | EXPO only, CL26 |
| `gskill-ddr5-6000-expo-ull_99999999.spd` | `20358e9a3654c389` | `0xAC67` match | UDIMM, 16 Gb, x8 | EXPO only, **two profiles** |
| `patriot-ddr5-6000-96gb_99999999.spd` | `151d4a43de2d1eee` | `0x02E9` match | UDIMM, 24 Gb, x8 | EXPO + XMP, both 2 profiles |
| `teamgroup-ddr5-7200_0202cbf7.spd` | `8957fad2a584e68c` | `0x6FEC` match | UDIMM, 24 Gb, x8 | EXPO + XMP, 7200 + 6000 |
| `teamgroup-ddr5-8800-cudimm_99999999.spd` | `754b3a70d6aede73` | `0x4762` match | **CUDIMM**, 24 Gb, x8 | XMP only, 8800 + 9500 |

Full sha256 values:

```
biwin-ddr5-6000_99999999.spd              00014112383ef10cb0e9e738b08e48cec599ff04f5e2f346dfaead877c9e1a61
gskill-ddr5-6000-expo_99999999.spd         7f9e6a024f6e7d6bffa42e822e26a901db451bd27f0de462120781e663d08410
gskill-ddr5-6000-expo-ull_99999999.spd    20358e9a3654c3898fb9663bda0492d67bb1212388ad9372b401432ebc9f0c5b
patriot-ddr5-6000-96gb_99999999.spd       151d4a43de2d1eee003f3e35627751a9099355e0809bdbe2091df4758153515d
teamgroup-ddr5-7200_0202cbf7.spd          8957fad2a584e68ca2a28d84909ddbe001b78ddccb2a0901111d212fd703eadd
teamgroup-ddr5-8800-cudimm_99999999.spd   754b3a70d6aede737e6835d1d89cf99d9a0cda96b14c81cc4425ed381c7ebbea
```

All six are 1024 bytes, matching the DDR5 SPD image size asserted by `fixture_is_1024_bytes`.

### Confirmed by decode

Base configuration CRC, computed over bytes 0-509 with the same CRC-16/XMODEM primitive verified in Phase 2 and compared against the stored bytes 510-511: computed equals stored on **all six**. Vendor block CRCs likewise: EXPO block CRCs `0x1B6E` (Biwin), `0xE5C0` (G.Skill EXPO), `0x67EB` (G.Skill ULL), `0xBBDE` (Patriot), `0x4590` (TeamGroup 7200) all recompute exactly.

**Rated timings, the value oracle.** Each profile's decoded rating is checked against what the product is sold as:

- Biwin DDR5-6000 CL28: 6000 MT/s (tCK 333 ps), CL28, tRCD/tRP 11655 ps (35 clocks), tRAS 23976 ps (72 clocks), 1.400 V. Identical under XMP and EXPO.
- G.Skill DDR5-6000 EXPO: 6000 MT/s, CL26, tRAS 31980 ps (96 clocks), 1.400 V.
- G.Skill DDR5-6000 **EXPO ULL**: profile 1 and profile 2, both CL30 / 6000 MT/s / 1.350 V, differing **only in tRAS** — profile 1 at 10660 ps (32 clocks), profile 2 at 31980 ps (96 clocks). This matches the published rating of the ULL kit, which is sold as `30-38-38-32` against a standard EXPO `30-38-38-96`.
- Patriot DDR5-6000 96 GB: two profiles under both XMP and EXPO — CL28 / tRAS 33300 ps at 1.400 V, and CL36 / tRAS 25308 ps at 1.350 V.
- TeamGroup DDR5-7200: profile 1 at 7200 MT/s (tCK 277 ps) CL34 / tRAS 23268 ps, profile 2 at 6000 MT/s CL38 / tRAS 25974 ps. Identical under XMP and EXPO.
- TeamGroup DDR5-8800 CUDIMM: XMP only, 8800 MT/s (tCK 227 ps) CL42 and 9500 MT/s (tCK 211 ps) CL44, both 1.450 V.

**Two observations that are findings about the sample set, not about the decoder:**

- **G.Skill's two AMD-oriented kits carry EXPO and no XMP block at all** (`xmp: Absent`), while the Biwin, Patriot and TeamGroup kits carry both. AMD-only branding does not imply EXPO-only in general — Patriot and TeamGroup ship dual-format — so this is a per-vendor product decision and not a pattern the decoder is entitled to generalise.
- **The CUDIMM resolves to the explicit not-yet-decoded result**, `module_specific: NotYetDecoded(Cudimm)`, exactly as the deferred-scope policy requires. Its XMP block decodes normally (two profiles, CRC-clean). This is the first fixture of a non-UDIMM module type, and it confirms the deferral is a real dispatch outcome on a real module rather than an untested branch.

### Linter findings on the new set, recorded rather than suppressed

These six are **not** added to the `fixture_lints_clean` invariant, and the reason is the point of the set. The original fixture still lints clean under the full Phase 10 rule set and that test is untouched. The new modules lint as follows, and the distribution is the finding:

| Fixture | Findings | Severity mix |
| --- | --- | --- |
| Biwin | 3 | 3 warnings |
| G.Skill EXPO | 6 | 6 warnings |
| G.Skill **EXPO ULL** | 10 | 9 warnings, **1 error** |
| Patriot | 3 | 3 warnings |
| TeamGroup 7200 | 3 | 3 warnings |
| TeamGroup CUDIMM | 4 | 3 warnings, 1 info (`non-standard-data-rate`, the 9500 bin) |

**`timing-ordering-violation` on the ULL kit is a real rule-versus-product disagreement and is left open on purpose.** The rule asserts `tRAS >= tRCD`. The G.Skill EXPO ULL profile 1 decodes tRAS 10660 ps against tRCD 12659 ps, and G.Skill sells that exact rating (`30-38-38-32`). Either the ordering rule needs an EXPO ULL exception, or it is reporting something about the profile that is intended. This document does not decide that; it records that a widely-shipped, vendor-certified profile trips an error-severity rule, which is on its face a defect in the rule, the severity, or the assumption that the ordering is universal. It is left failing rather than special-cased away because the honest state of the question is open.

**`non-integer-clock-timing` and `non-integer-cas-latency` appear on five of six modules and are treated as a tolerance question, not a module defect.** The encoded ps values are not exact whole multiples of tCK — 12659 ps against a 333 ps tCK, for instance. That pattern across five independent vendors is a decode-tolerance characteristic, not five modules with sub-clock timings.

### Why this does not widen any correctness claim

The set adds modules, not claims. What is now enumerable is seven real modules across five vendors and four module-relevant shapes (UDIMM 16 Gb, UDIMM 24 Gb, CUDIMM, and dual- versus EXPO-only profile blocks). What remains deferred is unchanged: SODIMM, RDIMM and LRDIMM module-specific decoding; full JEDEC sub-grade-table conformance; the tFAW >= 4 x tRRD_S ordering; and the remaining profile timings inside the CRC-confirmed region that are not surfaced. The EXPO 1.2 / EXPO ULL subtimings — `tREFI`, `tRRD_S`, `tWR` and the VDDP voltage lock — are **not decoded at all**, and the ULL fixture is the evidence for that gap rather than a closure of it: the ULL profile is visible only through its effect on the standard tRAS field.

## Reference markers

All carried markers are now closed: the main configuration CRC in Phase 2, the module manufacturer ID and manufacturing date in Phase 5, and the rated speed and the XMP / EXPO section CRCs in Phase 9a above. The semantic-linter pass over the now-decoded XMP/EXPO profiles is also done (Phase 9b, above). The remaining unimplemented surface is not a reference marker but deferred decode work, gated on real fixtures: the SODIMM / RDIMM / LRDIMM module-specific blocks.
