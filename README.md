<p align="center">
  <img src="https://snmplens.com/assets/img/SnmpLens.png" alt="SnmpLens" width="96" />
</p>

<h1 align="center">SnmpLens community catalogue</h1>

<p align="center">
  Simulator models and dashboard presets, contributed by whoever has the equipment.
  <br />
  For <a href="https://github.com/Wasabules/SnmpLens">SnmpLens</a>, the SNMP MIB browser and monitoring desktop application.
</p>

---

## What is here

| Directory | What it holds |
| --- | --- |
| `models/` | **Simulator models** — a device SnmpLens can answer SNMP as, on your own machine: its objects, its tables, its notifications, and what a real one answered when somebody walked it |
| `presets/` | **Dashboard presets** — what to poll on one kind of equipment, how often, and how to draw it |

Both are data. Neither carries anything that executes: a model picks from a frozen vocabulary the application owns, and so does a preset. Every pull request is validated with **SnmpLens's own code** — the same parser the application imports with — so what lands here is what your copy will accept.

## Using a model

1. Download the model's folder as a ZIP (**Code → Download ZIP**, or clone this repository).
2. In SnmpLens: the **Simulator** in the header, then **New device → Import models…** and pick the ZIP or the `model.json`.
3. Make a device from it, start it, and **Add as target**. It answers SNMP on a loopback address, so you can browse, poll, chart and receive traps from equipment you do not have in front of you.

## Using a preset

1. Download the `.json` file.
2. In SnmpLens: **Settings → Presets → Add a preset**, then bind it to a target from the same screen. The dialog states what it will poll and how often before anything is sent.

## Contributing

Read [CONTRIBUTING.md](CONTRIBUTING.md). The short version:

- **A model is easiest to make from a real device.** SnmpLens records one for you — *Simulator → New device → Record a device…* — and exports it as a package you drop into `models/<id>/`.
- **Anonymise a recording before you open a pull request.** A walk carries the device's name, its addresses and its neighbours. It never carries credentials: the recorder refuses to write the agent's own subtrees, where a real device keeps its community strings and SNMPv3 users.
- **Every pull request is checked automatically** with `cataloguecheck`, and read by a person for the things a checker cannot see.

## Licence

Everything in `models/` and `presets/` is released under [CC0 1.0](LICENSE): no rights reserved, no attribution required. Contributing means agreeing to that — it is what lets anyone ship, edit and redistribute these files, including inside SnmpLens itself.

The SnmpLens application is MIT-licensed and lives [in its own repository](https://github.com/Wasabules/SnmpLens).
