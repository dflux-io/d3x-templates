# d3x-templates

Declarative YAML flows and suites for **dflux Runner** (`d3x-run`).

This repository is the **corpus**, not the engine. Each file is a state machine the runner drives against a real 5G core — tinycore, Open5GS, free5GC, or a vendor AMF/SMF/UPF. The runner binary is separate and proprietary; these templates are Apache-2.0 so you can read them, copy them, and write your own.

A flow succeeded **if and only if** the runner says so: the FSM reached a state the template names as its correct outcome, **and** the end-of-flow assertions (`final_checks`) confirm the right messages were sent and received. Gate on `d3x-run run-flow`'s exit code (0 pass, 1 fail, 3 unsupported). `all_passed` alone is not that answer.

## Quick start

```bash
git clone git@github.com:dflux-io/d3x-templates.git
# runner binary comes from the d3x suite (d3x-run)

d3x-run lint -templates ./d3x-templates

d3x-run run-flow \
    -flow registration \
    -templates ./d3x-templates \
    -c lab.yaml \
    -s subscribers.yaml \
    -trace
```

`-templates` is required. The loader walks the directory, picks up every `kind: flow` and `kind: suite` YAML, and resolves `-flow` / `-suite` **by the YAML `name:` field**, not by file path.

```bash
d3x-run run-suite \
    -suite gnb-compliance \
    -templates ./d3x-templates \
    -c lab.yaml \
    -s subscribers.yaml
```

## Layout

| Directory | What it drives |
|---|---|
| `gnb/` | NGAP/NAS from a gNB: registration, session, handover, paging, SMS, security, identity |
| `amf/` | Server-mode AMF (the runner *is* the AMF) |
| `nrf/` | Nnrf NFManagement, Discovery, AccessToken, Bootstrapping, subscriptions |
| `ausf/` | Nausf UE authentication, SoR/UPU protection |
| `udm/` | Nudm SDM / UECM / UEAU |
| `smf/` | Nsmf PDU session + SMF-side PFCP |
| `upf/` | N4 PFCP association, session, QER/URR, metrics |
| `pcf/` | Npcf SM/UE/AM policy, policy authorization |
| `nssf/` | Nnssf NS Selection / NSSAI availability |
| `bsf/` | Nbsf management |
| `nudr/` | Nudr influence / provisioning |
| `namf/` | Namf communication (N1N2 transfer, status subscribe) |
| `sbi/` | Generic SBI client/server and load |
| `pfcp/` | PFCP client/server setup and fuzz |
| `diameter/` | S6a / Gx / Rx |
| `rest/` | Edge-admin REST |
| `edge/` | Edge policy (Diameter lifecycle) |
| `multinf/` | Multi-protocol (NGAP + Diameter on one UE) |
| `suites/` | Multi-flow packs (compliance, security, smoke, load) |

`nrf/README.md` is the operational manual for the NRF suites (bring-up, JWT key, persistence / mTLS / concurrency runners).

## Flows and suites

A **flow** is one procedure: states, transitions, `send` / `check` / `extract` / `uplane_start` actions, and optional `final_checks`. A **suite** is an ordered list of flow names, with per-step workload, `stop_on_failure`, and cleanup steps.

`final_checks` is where a flow **states a claim**. Without it the runner can only say that no check failed — including a flow that never reached a terminal. Most `gnb/` flows carry them; that is what v1 coverage asserts. A core not supporting a scenario is a fine and expected result: it is the answer, not a failure of the template.

## Authoring

1. Copy the closest existing flow.
2. Give it a unique `name:` (the catalog is name-keyed; duplicates fail load).
3. Put `final_checks` on anything you want the runner to verdict. An assertion never shown to fail is not evidence — prove a new check discriminates.
4. `d3x-run lint -templates .` must accept the whole tree. One bad YAML aborts the catalog.

Do not put scoring, classification, or “did it really pass?” logic outside the runner. If the verdict needs a fact, put the assertion in the template.

## Compatibility

These templates were authored against tinycore and run against Open5GS and free5GC as well. A flow the runner cannot encode, or cannot decode a real core’s reply for, is a **runner** bug — not a template caveat and not a finding about the core.

## License

Apache License 2.0. See [LICENSE](LICENSE).
