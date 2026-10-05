# Lamzu Paro Aurora — vendor-table notes

No hardware capture yet. Everything below comes from Lamzu's own Aurora web
configurator (`https://www.lamzu.net/`, `Config/env-models.json`,
`ModelEN: PARO`), corroborated by community udev rules
(passionofcrisis/lamzu-webdriver-aurora-linux-fix, commit "Add Paro Aurora").
The catalog entries are vendor-table only until a unit is exercised on
hardware.

## Enumeration

Vendor id `0x37b0` — Lamzu's own id, introduced with the Inca 8K. Product ids
for this model:

| Product id | Role | USB product string | Verified |
| --- | --- | --- | --- |
| `0x0007` | Mouse on its cable | `LAMZU LAMZU PARO` | Vendor table only |
| `0x000d` | 1K receiver | `LAMZU LAMZU PARO 1K Receiver` | Vendor table only |
| `0x000e` | 8K receiver | `LAMZU LAMZU Aurora 8K Receiver` | Vendor table only |
| `0x0008` | Mouse DFU bootloader — **not a mouse** | — | Vendor table only |
| `0x0004` | 1K receiver DFU bootloader — **not a mouse** | — | Vendor table only |
| `0x0002` | 8K receiver DFU bootloader — **not a mouse** | — | Vendor table only |

The three bootloader ids (`DeviceBLPID` 0008, `Receiver1KBLPID` 0004,
`Receiver4K8KBLPID` 0002) are the identities the mouse and dongles take while
flashing firmware. They do not speak the config protocol and are excluded from
the catalog on purpose — same treatment as the Inca's `0x000a`/`0x0002`.

## Protocol fit

The Aurora table flags the Paro exactly like the Inca: `IsNewProtocol: 1`,
`IsCompx: 0`, `DPIMax: 30000`. It rides the same vendor id and is expected to
answer the identical CompX feature-report page/command framing on target
`0x02`, so it shares the `LAMZU_INCA_PRODUCTS` catalog and the `lamzu` WebHID
driver with no protocol changes.

Rate lists from the table:

| Connection | Table field | Rates |
| --- | --- | --- |
| Cable | `PollingRateWired` | 125;250;500;1000 |
| 1K receiver | `_1KDonglePollingRate` | 125;250;500;1000 |
| 8K receiver | `_8KDonglePollingRate` | 500;1000;2000;4000;8000 |

`DPIMax` 30000 is already the driver default, so no `maxDpi` override.

## Still to verify on hardware

- That each of `0x0007`/`0x000d`/`0x000e` answers the config channel
  (MI_02, usage page `0xffff`, usage `0x0000`) with the CompX framing.
- The rate split on the wire (the Inca answered 1000 Hz as `0x01` on the
  cable and `0x40` on the 8K receiver; the Paro is assumed to match).
- Firmware/battery/dongle reads and a write round-trip (DPI stage set +
  read-back) before any entry is marked `verified`.
