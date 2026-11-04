# Diameter flows

Two kinds of flow live here, and they do not talk to the same far end.

**SCP echo flows** (`diameter_s6a_*_success`, `*_relay`, `diameter_gx_ccr_cca_*`,
`diameter_rx_*`) run against an SCP in echo mode: Runik sends a request, the SCP
reflects it, Runik answers its own reflected request, the SCP reflects the
answer. One rk process plays both halves. Point one at a real HSS or PCRF and it
waits for an echo that never comes.

**Request/answer pairs** expect a real answer, from a real core or from Runik
in a second process:

| Runik plays | Flow | Far end (Runik) | Exchange |
|---|---|---|---|
| MME | `diameter_s6a_mme_air` | HSS `diameter_s6a_hss_air_responder` | S6a AIR → AIA 2001 |
| MME | `diameter_s6a_mme_ulr` | HSS `diameter_s6a_hss_ulr_responder` | S6a ULR → ULA 2001 |
| PGW | `diameter_gx_pgw_ccr_session` | PCRF `diameter_gx_pcrf_ccr_responder` | Gx CCR-I / CCR-U / CCR-T → CCA 2001, CC-Request-Type mirrored |
| PGW | `diameter_gx_pgw_ccr_rar_session` | PCRF `diameter_gx_pcrf_ccr_rar_responder` | Gx CCR-I → CCA, PCRF RAR → RAA 2001, CCR-U, CCR-T |

Runik as the PCRF keeps every Gx session it serves (Session-Id, last
CC-Request-Number) and holds the PGW to the order: a CCR-UPDATE or
CCR-TERMINATION on no live session is answered 5002
DIAMETER_UNKNOWN_SESSION_ID, a second CCR-INITIAL on a live one 5012, a
CC-Request-Number that does not increase is counted, a RAR goes only to a live
session and its RAA must answer one. Every outcome is a `gx_*` metric of the
PCRF's report (`gx_violations` is the sum of the order violations,
`gx_sessions_open` what is still open); both PCRF flows assert
`gx_violations equals 0`. A PCRF flow that must act on one CC-Request-Type
names the typed event `CCR.Initial` / `CCR.Update` / `CCR.Termination` /
`CCR.Event`; a flow that names none gets every CCR as `CCR`.

The responders are ambient: one server context answers every request and
re-enters `waiting`, and the flow completes once its peer has been quiet for
5s. They are judged on the traffic they answered (`final_checks`), and on the
checks they hold every request to — a request missing what the check names gets
no answer. The client flows' `final_checks` are count-free (`failed equals 0`,
`completed >= 1`), so the same claim holds at `-repetitions 1` and at 10k.

## Runik vs Runik

Diameter is one stack per rk env, so the two ends are two rk processes. The env
files are in the d3x repo, `runik/config/`. Start the responder first — it waits
for the peer's CER (`responder.timeout`):

```bash
# HSS (pick the responder for the exchange), then the MME
rk run-flow -flow diameter_s6a_hss_air_responder -templates . \
    -c <d3x>/runik/config/lab-diameter-hss.yaml -timeout 60s &
rk run-flow -flow diameter_s6a_mme_air -templates . \
    -c <d3x>/runik/config/lab-diameter-mme-hss.yaml \
    -subscriber-seed 1 -repetitions 1000 -rate 200

# PCRF, then the PGW
rk run-flow -flow diameter_gx_pcrf_ccr_responder -templates . \
    -c <d3x>/runik/config/lab-diameter-pcrf.yaml -timeout 60s &
rk run-flow -flow diameter_gx_pgw_ccr_session -templates . \
    -c <d3x>/runik/config/lab-diameter-pgw-pcrf.yaml \
    -subscriber-seed 1 -repetitions 1000 -rate 200
```

Each side's exit code is its own verdict. The requests carry the UE's IMSI
(User-Name, Subscription-Id), so the client run needs subscribers: `-s`,
`-gen-subscriber`, or `-subscriber-seed`.

The HSS answers carry no authentication vectors and no Subscription-Data; the
PCRF installs no charging rules. These pairs exercise the Diameter exchange —
CER/CEA, routing, Session-Id, the answer each request gets — not 4G AKA or
policy content.
