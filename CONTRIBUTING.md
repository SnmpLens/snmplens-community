# Contributing to the catalogue

Two kinds of file are welcome: **simulator models** and **dashboard presets**. Both are data read through a frozen vocabulary, and both are validated on every pull request with SnmpLens's own parser, so the check and the application cannot disagree.

---

## A simulator model

A model is what a simulated device answers: its objects and tables, the notifications it can send, and — the part only you can provide — what a real one actually answered.

### The easy way: record the device you have

SnmpLens does the work:

1. **Simulator → New device → Record a device…**, and give it the target and community of the real equipment.
2. It walks `.1.3.6.1` and `.1.0.8802` and writes a package: every object, with the types the agent sent rather than the text it printed.
3. **Export model…** writes that package as a ZIP.
4. Unzip it into `models/<id>/` here, anonymise it (below), and open a pull request.

### What a package holds

```
models/<id>/
  model.json        the identity: id, name, vendor, category, description, sysObjectID
  oids.json         objects written by hand — or oids/*.json, split as you like
  traps.json        the notifications the model can send
  walks/*.snmprec   what a real device answered (snmpsim format, or `snmpwalk -On` output)
  icon.png          optional, named by model.json
```

Nothing else is read. A `.json` the format does not know is reported rather than passed over, because `traps.json` misspelt would otherwise leave a device with no notifications and nothing saying why.

Two rules decide what a device answers when both halves describe the same OID:

- **What is written wins over what was recorded**, OID by OID — and IF-MIB as a whole when `model.json` says `interfaces`, since a written `ifTable` mixed with recorded rows gives `ifNumber` two answers.
- **The agent's own subtrees are never served**: the `snmp` group and everything under `1.3.6.1.6.3`. On a real device those hold the engine ID, the SNMPv3 user names and the community table. They are stripped when recording and refused on import.

### Bounds

| What | Limit |
| --- | --- |
| `model.json`, and each written OID file | 1 MB |
| Each walk | 16 MB |
| Objects a package's walks record between them | 65 536 |
| Entries in an imported ZIP / unpacked size | 64 / 40 MB |
| Icon | PNG, JPEG or GIF, 256 KB, 512 px a side |

### Anonymise a recording — this is the one that matters

A walk is a photograph of a real device on a real network. Before it goes into a public repository, replace:

- **Names**: `sysName`, `sysLocation`, `sysContact`, and any host name in a table (`lldpRemSysName`, `tcpConnTable` peers).
- **Addresses**: everything in `ipAddrTable`, `ipNetToMediaTable`, the routing tables, and LLDP management addresses. Use the ranges reserved for documentation: `192.0.2.0/24`, `198.51.100.0/24`, `203.0.113.0/24`, `2001:db8::/32`.
- **Serial numbers and asset tags**: `entPhysicalSerialNum`, `entPhysicalAssetID`, and whatever your vendor puts in its own MIB.
- **Anything that identifies your organisation**, including in descriptions.

What you do **not** have to remove, because it was never written: community strings, SNMPv3 users and the VACM tables. The recorder does not read the subtree they live in.

Keep the MAC addresses and interface names if they are ordinary — they are what makes the model useful — but change them too if they identify you.

### Naming

- `<id>` is lowercase, digits and hyphens: `acme-switch-2400`.
- The folder is named after the `id` inside `model.json`. The check refuses a mismatch, so that a reader who sees a model in the application finds the folder it came from.
- `name`, `vendor` and `description` are for a person choosing from a list of a hundred. Say what the device is and what it answers.

---

## A dashboard preset

A preset says what to poll on one kind of equipment, how often, and how to draw it. It makes SnmpLens emit SNMP requests to the operator's own devices, at OIDs the file chose — which is why the format is narrow and the cost is stated before anything is bound.

- **OIDs are numeric**, never names: a name would depend on which MIBs the reader happens to have loaded.
- **Widgets pick a kind** from the list the application owns (`value`, `rate`, `chart`, `grid`, `status`, `map`). A kind it cannot draw is said out loud rather than skipped.
- **Discovery beats hand-written instance numbers.** `"discover": {"walk": "1.3.6.1.2.1.2.2.1.2"}` with `{#}` in the OIDs makes one preset fit a 24-port switch and a 48-port one; the walk happens once, when the preset is bound.
- **A layout is worth adding**: `"layout": {"x": 0, "y": 1, "w": 6, "h": 2}` on a twelve-column grid. Without one the widgets reflow in declaration order.
- **Thresholds are for what the MIB decides**, not what the operator decides: a UPS on battery, a disk at 95%. Not a percentage on a link you have never seen.

`cataloguecheck` prints what each preset would cost — widgets, OIDs, interval. That figure is part of the review.

---

## What is refused

- Credentials of any kind, in any file.
- Vendor MIB files. They carry their own licences; this catalogue holds what a device ANSWERS, not the documents describing it.
- Anything executable, or a file format outside the two above.
- A recording you are not allowed to publish. If the device belongs to an employer or a customer, ask first.

## The review

1. **The automatic check** runs `cataloguecheck` from the SnmpLens repository against this one: every model is built exactly as the application builds it at import, and every preset is validated and costed. A failure names the file and the reason.
2. **A person reads it** for what a checker cannot see: whether the recording is anonymised, whether the description says what the device is, and whether the model is worth carrying.

## Licence

Contributions are released under [CC0 1.0](LICENSE) — no rights reserved. Opening a pull request means you have the right to publish the file and agree to that licence.
