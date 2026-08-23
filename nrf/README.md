# NRF Sanity Flows — How to Run

This directory contains d3x-run flows that drive sanity tests
against an NRF over REST/HTTP-2 (tinycore's NRF is the reference
implementation they were authored against).

Suites are organised **by Nnrf service** (matching TS 29.510 — see plan §2.7):

**Run via `run-suite`:**

- **`nrf_smoke`** — CI gate; 5 happy-path flows (one per service). **Renamed from `nrf_sanity_tranche_a`.**
- **`nrf_nfm`** — Nnrf_NFManagement depth + negatives (13 flows, NFM-01..14).
- **`nrf_oauth`** — Nnrf_AccessToken depth (7 flows, OAUTH-01..07).
- **`nrf_bstr`** — Nnrf_Bootstrapping (3 flows, BSTR-01..03).
- **`nrf_disc`** — Nnrf_NFDiscovery filters + negatives (8 flows, DISC-01..08).
- **`nrf_sub`** — NFM Subscriptions CRUD (4 flows, SUB-01..04).
- **`nrf_problem`** — RFC 7807 ProblemDetails shape (1 flow, PROB-01).

**Driven by a shell runner (not `run-suite` — see "Shell harness runners" below):**

- **`scripts/nrf-restart.sh`** — persistence / restart-survival (PERSIST-01..04).
- **`scripts/nrf-longrun.sh`** — heartbeat-FSM + rate-limit + token-expiry (LONG-01..04).
- **`scripts/nrf-mtls.sh`** — TLS / mutual-TLS handshake (MTLS-01..06).
- **`scripts/nrf-notify.sh`** — subscription notification round-trip (SUB-05 / NOTIFY-01).
- **`scripts/nrf-oauth-enforced.sh`** — OAuth2 enforcement (OAUTH-ENF-01).
- **`scripts/nrf-concurrency.sh`** — concurrent-request burst, N=100 (CONC-01/02/04/06/07, memory + sqlite).

The suite table below is the inventory of what is runnable from this directory.

---

## What's here today

Individual flow files aren't enumerated here (there are ~70). This table
summarizes what's runnable.

**Run-suite suites** (`run-suite -suite <name>`):

| Suite | Flows | Coverage |
| --- | --- | --- |
| `nrf_smoke` | 5 | CI gate — one happy-path flow per service |
| `nrf_nfm` | 13 | NFM register / get / patch / delete + negatives (NFM-01..14) |
| `nrf_oauth` | 7 | Nnrf_AccessToken depth (OAUTH-01..07) |
| `nrf_bstr` | 3 | Nnrf_Bootstrapping (BSTR-01..03) |
| `nrf_disc` | 8 | NFDiscovery filters + negatives (DISC-01..08, incl. routing-indicator) |
| `nrf_sub` | 4 | NFM Subscriptions CRUD + negatives (SUB-01..04) |
| `nrf_problem` | 1 | RFC 7807 ProblemDetails shape across negative paths (PROB-01) |

**Shell-runner classes** (each launches its own NRF — see "Shell harness runners" below):

| Runner | Class | Flows |
| --- | --- | --- |
| `scripts/nrf-restart.sh` | persistence / restart-survival (PERSIST-01..04) | 7 `persistence_*.yaml` + 1 `.tmpl` |
| `scripts/nrf-longrun.sh` | heartbeat-FSM + rate-limit + token-expiry (LONG-01..04) | `heartbeat_timeout_fsm`, `heartbeat_patch_resets_clock`, `oauth_token_expired` (+ in-runner rate-limit burst) |
| `scripts/nrf-mtls.sh` | TLS / mutual-TLS handshake (MTLS-01..06) | 6 (`tls_handshake_*`, `mtls_*`) |
| `scripts/nrf-notify.sh` | subscription notification round-trip (SUB-05 / NOTIFY-01) | `notify_receiver.yaml` (`type: server`) |
| `scripts/nrf-oauth-enforced.sh` | OAuth2 enforcement (OAUTH-ENF-01) | `oauth_required_mode.yaml` |
| `scripts/nrf-concurrency.sh` | concurrent-request burst N=100 (CONC-01/02/04/06/07) | 4 `conc_*.yaml`, each burst ×100 on memory + sqlite |

**Lab configs** live with the runner (`runner/config/lab-nrf.yaml` in the d3x suite, default `:8080` REST binding) plus per-class variants `lab-nrf-persistence.yaml`, `lab-nrf-longrun.yaml`, `lab-nrf-mtls.yaml` (`:8443` TLS), `lab-nrf-notify.yaml` (`:39999` rest-listen). The shell runners listed above also live in that suite (`runner/scripts/nrf-*.sh`), not in this repository.

---

## Prerequisites

You need:

1. The **runner binary** (`d3x-run`) from the d3x suite.
   ```bash
   cd /path/to/d3x
   make run
   ```
   **Rebuild after pulling** — flows use engine features added over time
   (e.g. `extract value:` / `$jwt_decode`); a stale binary fails template
   load with errors like `extracts[0] requires field and store`.

2. An **NRF** to drive. tinycore's NRF is the reference:
   ```bash
   cd /path/to/d3x
   make core
   ```

3. An **RSA private key** for NRF's OAuth2 JWT signing. Without it, NRF
   returns `500 server_error "token signing not configured"` and the
   OAuth flows fail.
   ```bash
   openssl genrsa -out /tmp/nrf-jwt.pem 2048
   ```

4. **Subscribers file** — runner's engine needs at least one subscriber
   even for non-UE flows (one is acquired per flow run, then released).
   The shipped `config/subscribers.yaml` is fine.

5. **`uuidgen`** — the NRF launch commands below (and the `scripts/nrf-*.sh`
   runners) pass `-nrf-id "$(uuidgen)"`. It ships in `uuid-runtime` on
   Debian/Ubuntu (`sudo apt-get install -y uuid-runtime`) and `util-linux`
   on Fedora/Arch. If it's missing and you can't install it, any UUID
   source works — substitute one of:
   ```bash
   -nrf-id "$(cat /proc/sys/kernel/random/uuid)"   # Linux kernel RNG, no install
   -nrf-id "$(python3 -c 'import uuid; print(uuid.uuid4())')"
   ```

---

## Bring up the NRF

```bash
# In its own terminal — leave running for the duration of the test.
# -nrf-id is required so the bootstrapping flow's nrfInstanceId
# assertion has something to match.
/path/to/d3x-core/bin/nrf \
    -port 8080 \
    -db-driver memory \
    -jwt-key /tmp/nrf-jwt.pem \
    -nrf-id "$(uuidgen)"
```

Confirm it's up:

```bash
curl -fsS http://127.0.0.1:8080/health
# → {"status":"healthy"}

curl -fsS http://127.0.0.1:8080/nnrf-bstr/v1/bootstrapping | python3 -m json.tool
# Should show _links, status: OPERATIVE, nrfInstanceId, oauth2Required
```

---

## Run one flow (fastest signal)

```bash
d3x-run run-flow \
    -flow nrf_bstr_get \
    -templates /path/to/d3x-templates \
    -c lab-nrf.yaml \
    -s subscribers.yaml
```

Expected tail of output:

```
Checks:    4/4 passed
RESULT: PASS
Report:    <execution-id>
```

If this fails, the rest will too — fix this first.

---

## Run the smoke suite (CI gate)

```bash
d3x-run run-suite \
    -suite nrf_smoke \
    -templates /path/to/d3x-templates \
    -c lab-nrf.yaml \
    -s subscribers.yaml
```

Expected output:

```
Suite cycle 1/1
Suite:    nrf_smoke
Duration: ~270ms
  PASS  bootstrapping              nrf_bstr_get
  PASS  nfm_lifecycle              nrf_nfm_register_then_deregister
  PASS  register_variations        nrf_nfm_register_201_location
  PASS  oauth_basic                nrf_oauth_token
  PASS  oauth_headers_and_negative nrf_oauth_token_headers
RESULT: PASS
```

`final_checks` pin `flows_succeeded == 5`, `flows_failed == 0`,
`checks_failed == 0`.

## Run a per-service suite

```bash
./bin/d3x-run run-suite -suite nrf_nfm     -templates templates -c config/lab-nrf.yaml -s config/subscribers.yaml
./bin/d3x-run run-suite -suite nrf_oauth   -templates templates -c config/lab-nrf.yaml -s config/subscribers.yaml
./bin/d3x-run run-suite -suite nrf_bstr    -templates templates -c config/lab-nrf.yaml -s config/subscribers.yaml
./bin/d3x-run run-suite -suite nrf_disc    -templates templates -c config/lab-nrf.yaml -s config/subscribers.yaml
./bin/d3x-run run-suite -suite nrf_sub     -templates templates -c config/lab-nrf.yaml -s config/subscribers.yaml
./bin/d3x-run run-suite -suite nrf_problem -templates templates -c config/lab-nrf.yaml -s config/subscribers.yaml
```

> **`nrf_bstr` needs the NRF started with `-oauth2`.** BSTR-02 asserts
> bootstrapping stays public *under enforcement*, so against the default
> (non-`-oauth2`) NRF that flow fails. Restart with
> `-oauth2 -jwt-key … -nrf-id …` before running `nrf_bstr` (BSTR-01/03
> are mode-agnostic). The other suites above run against the plain NRF.

These are now full depth/negative suites (flow counts in "What's here
today" above), each gating pass/fail via `final_checks`
(`flows_succeeded`/`flows_failed`/`checks_failed`). They run against a
plain NRF (`-db-driver memory`, `-jwt-key`); the cross-cutting classes
(persistence, longrun, mTLS, notify, oauth-enforced) need their shell
runners instead — see below.

---

## Shell harness runners (`scripts/nrf-*.sh`)

Some test classes can't run under plain `run-suite` — they need the NRF
launched with specific flags, restarted mid-test, or driven by a
concurrent client. Those ship as **shell runners** under `scripts/`
(persistence, longrun, mTLS, notification, oauth-enforced, concurrency —
each documented below). They all share the same invocation convention:

```bash
cd /path/to/d3x-run                 # run from the repo root
NRF_BIN=../d3x-core/bin/nrf scripts/nrf-<class>.sh
```

- **`NRF_BIN=... cmd`** is the shell "inline env var" form — it sets
  `NRF_BIN` for that one command only (not exported to your shell). Each
  runner reads it as `NRF_BIN="${NRF_BIN:-$REPO_ROOT/../d3x-core/bin/nrf}"`,
  i.e. **use the value if set, else fall back to the default** (the NRF
  binary in the sibling `d3x-core` checkout). On the standard
  side-by-side layout the prefix matches the default, so it's optional;
  pass it when your binary lives elsewhere (e.g. `NRF_BIN=/tmp/nrf …`).
- **Build the NRF first:** `go build -o ../d3x-core/bin/nrf ../d3x-core/cmd/nrf`.
- **Other overridable env vars** (same `VAR=value` form): `Runner_BIN`
  (default `bin/d3x-run`), `PORT`, `TEMPLATES`, `CONFIG`, and a
  few class-specific ones noted per section.
- Every runner launches its own NRF and **kills it by PID** (never
  `pkill -f`), so you don't manage the NRF lifecycle yourself.

---

## Run the persistence suite (restart harness)

Persistence (PERSIST-01..04, plan §2.14) cannot run under `run-suite`:
each test is a **pair** of flows with an **out-of-process NRF restart**
in between, which runner's suite runner has no concept of. The shell
harness `scripts/nrf-restart.sh` is the real runner for this class — it
owns the NRF lifecycle (fresh sqlite DB → start → flow A → kill →
restart same DB → flow B → drop DB), one isolated DB per pair.

```bash
cd /path/to/d3x-run
NRF_BIN=../d3x-core/bin/nrf scripts/nrf-restart.sh
```

Expected tail:

```
[harness] PASS: persist01-nfm: state survived restart
[harness] PASS: persist02-sub: state survived restart
[harness] PASS: persist03-oauth: state survived restart
[harness] PASS: persist04-multi: state survived restart
[harness] passed: 4   failed: 0
```

The harness builds its own temp JWT key and launches the NRF with
`-db-driver sqlite -db-dsn <file> -jwt-key <pem>`. It kills the NRF
**only by the PID it launched** (never `pkill -f`, which would match the
harness's own command line). Env overrides: `NRF_BIN`, `Runner_BIN`,
`PORT`, `TEMPLATES`, `CONFIG`.

**Why the subscription pair is different.** NF-instance pairs use a
fixed client-chosen UUID in the `PUT` path, so flow A and flow B share a
hard-coded ID with no handoff. Subscription IDs are *server-assigned*
(POST returns them), and runner has no way to inject a value into a flow at
invocation time. So `persistence_sub_verify.yaml.tmpl` is a template:
the harness scrapes the `subscriptionId` from flow A's output and
renders it via `envsubst '${SUB_ID}'` into a temp templates dir before
running flow B. The `.tmpl` is **not** a runnable flow and is ignored by
runner's `.yaml`-only template loader.

---

## Run the longrun suite (wall-clock runner)

Long-running / wall-clock tests (LONG-01, LONG-02, LONG-04, plan §4.7.9)
are driven by `scripts/nrf-longrun.sh`, not `run-suite`, for two reasons:

- **Heartbeat flows are phase-coupled.** The NRF heartbeat monitor ticks
  every `-heartbeat` seconds from *server start*, but a flow's
  `on_timeout` delays are anchored at *register*. Run at an arbitrary
  phase (e.g. second in a long-lived-NRF suite), a tick could land in the
  wrong spot and a probe window would miss. So the runner launches a
  **fresh NRF with `-heartbeat 20` for each heartbeat flow**, pinning the
  phase (register ≈ server-start).
- **The rate-limit test needs a concurrent burst.** runner's per-flow FSM
  can't express "fire N, assert ≥1 tripped 429"; the runner fires a
  `curl --parallel` burst with a fixed `X-Real-IP` (shared limiter key).

```bash
cd /path/to/d3x-run
NRF_BIN=../d3x-core/bin/nrf scripts/nrf-longrun.sh
```

Expected tail (~2 min total — two ~30-50s heartbeat flows + a sub-second burst + a ~35s token-expiry flow):

```
[longrun] PASS: LONG-04-heartbeat-timeout: Checks:    5/5 passed
[longrun] PASS: LONG-01-heartbeat-reset: Checks:    5/5 passed
[longrun] LONG-02-ratelimit: 400 reqs → 203×200, 197×429 (197 with problem+json)
[longrun] PASS: LONG-02-ratelimit: rate limiter tripped with RFC 7807 ProblemDetails
[longrun] PASS: LONG-03-token-expiry: Checks:    5/5 passed
[longrun] passed: 4   failed: 0
```

The two heartbeat flows (`heartbeat_timeout_fsm.yaml`,
`heartbeat_patch_resets_clock.yaml`) use the `on_timeout` state primitive
as a delay ("wait N, then probe") — runner has no sleep action. They can be
run standalone against a manually-launched `-heartbeat 20` NRF with
`run-flow -timeout 90s`, but the runner is the supported path. Like the
persistence harness, the NRF is killed **by PID**, never `pkill -f`.

**LONG-03 (token expiry)** — `oauth_token_expired.yaml` — uses the
`$jwt_decode` template op to read the token's `exp` claim client-side:
asserts `exp` is in the future at issue (and `expires_in` matches the
NRF's `-token-expiry 30s`), then after a 35s `on_timeout` wait asserts the
same `exp` is now in the past. The runner launches the NRF with
`-token-expiry 30s -jwt-key <temp>`. No protected endpoint or token
introspection needed — `$jwt_decode` (which does NOT verify the signature,
it's a test-introspection helper) reads the claim directly.

---

## Run the mTLS suite (TLS handshake runner)

TLS / mutual-TLS handshake tests (MTLS-01..06, plan §2.15 / §4.7.7) run
via `scripts/nrf-mtls.sh`, not `run-suite`: each needs the NRF launched
with a different **server cert** (good / bad-SAN / expired) and
**client-auth policy** (none / require). The runner generates a throwaway
PKI into `.mtls-certs/` (gitignored), then per test launches a fresh TLS
NRF and runs the matching flow. The negative flows assert the handshake
*fails* — runner delivers the transport error as the synthetic `Error`
event and the flow checks `ue.LastError.Cause`.

```bash
cd /path/to/d3x-run
NRF_BIN=../d3x-core/bin/nrf scripts/nrf-mtls.sh
```

Expected tail:

```
[mtls] PASS: MTLS-01: Checks:    2/2 passed   # trusted CA → 200
[mtls] PASS: MTLS-02: Checks:    1/1 passed   # foreign CA → unknown authority
[mtls] PASS: MTLS-03: Checks:    1/1 passed   # bad SAN → host-validation error
[mtls] PASS: MTLS-04: Checks:    1/1 passed   # expired cert → expiry error
[mtls] PASS: MTLS-05: Checks:    2/2 passed   # valid client cert + require → 200
[mtls] PASS: MTLS-06: Checks:    3/3 passed   # no client cert + require → REFUSED
[mtls] PASS: MTLS-06N: flow correctly failed (rc=1) against client-auth=request
[mtls] passed: 7   failed: 0
```

**MTLS-06 asserts refusal, not one error string**, and **MTLS-06N is its
negative control.** Which error a refused client observes is nondeterministic —
the server writes a TLS alert and tears the connection down, so the client races
between reading the alert and erroring on its own in-flight write. The same
correct rejection has shown up as `remote error: tls: certificate required`, as
`write: broken pipe`, and as `http2: client conn could not be established`.
MTLS-06 therefore accepts the SET of refusal renderings, explicitly rejects
`connection refused` / `no such host` (which mean the harness is broken, not the
server refusing), and fails unconditionally if the handshake SUCCEEDS. MTLS-06N
re-runs the same flow against `-tls-client-auth request` and requires it to
fail — that is what proves the broadened set still discriminates rather than
merely tolerating everything.

runner reads the client TLS material (`ca_file` / `cert_file` / `key_file`)
from `config/lab-nrf-mtls.yaml` via paths relative to the repo root, so
the runner invokes runner from there. NRF is killed by PID, never `pkill -f`.

**MTLS-07 / MTLS-08 are not included** (still Blocked):

- **MTLS-07** (client cert from an untrusted CA): a Go TLS client filters
  its client cert against the server's advertised acceptable-CA list, so
  a cert signed by a CA the server doesn't trust is **never sent** — the
  server replies "certificate required" (identical to MTLS-06), not an
  `unknown_ca` rejection. Exercising server-side rejection of a
  presented-but-untrusted client cert needs a client that ignores the CA
  hint, which runner's static cert config can't express.
- **MTLS-08** (revoked client cert): the NRF has no CRL support yet.

---

## Run the notification round-trip (rest-listen)

SUB-05 / NOTIFY-01 (plan §2.13 / §4.2.5) is the one test where runner acts
as a **REST server**: the `notify_receiver.yaml` flow (`type: server`)
binds the `rest-listen` binding on `:39999` and waits for the NRF to POST
a status notification. `scripts/nrf-notify.sh` drives the round-trip:

```bash
cd /path/to/d3x-run
NRF_BIN=../d3x-core/bin/nrf scripts/nrf-notify.sh
```

```
[notify] subscription create → 201
[notify] NF register → 201 (NRF should now POST NF_REGISTERED to the listener)
[notify] PASS: NF_REGISTERED notification received + asserted: Checks: 2/2 passed
[notify] PASS: subscription notification round-trip passed (SUB-05 / NOTIFY-01)
```

The runner starts the NRF, backgrounds the server flow (binds `:39999`),
then via curl creates a subscription whose `nfStatusNotificationUri`
points at the listener and registers an NF — which makes the NRF fire an
`NF_REGISTERED` notification. The listener receives it, asserts the body
(`event == NF_REGISTERED`, `nfInstanceUri` present), and replies `204`.

Server-flow mechanics worth knowing (no other flow in this dir is a
server): an inbound request raises the event **`rest.<METHOD>.<URL>`**
(here `rest.POST./notify`), the inbound body resolves via `rest.body.*`,
and the flow replies by sending a message whose name ends in **`_Answer`**
with `{"status": 204}` — the http adaptor writes that back on the held
`ResponseWriter`. A `type: server` flow runs for its whole `-duration`,
so the runner triggers the notification inside that window. NRF killed by
PID, never `pkill -f`.

---

## Run the OAuth2-enforcement test (oauth-enforced)

OAUTH-ENF-01 (plan §4.7.10) proves the `-oauth2` middleware rejects
unauthenticated calls to protected services *and* accepts a valid bearer
token, while keeping bootstrap + the token endpoint public.

```bash
cd /path/to/d3x-run
NRF_BIN=../d3x-core/bin/nrf scripts/nrf-oauth-enforced.sh
```

```
[oauth-enf] phase 1: NRF (sqlite, NO -oauth2) — seeding consumer …
[oauth-enf] consumer registered (201); restarting NRF with -oauth2
[oauth-enf] phase 2: NRF (sqlite, -oauth2 -jwt-key) — consumer survives the restart
[oauth-enf] PASS: OAUTH-ENF-01: Checks: 7/7 passed
```

**The chicken-and-egg, resolved without a SUT change.** Under `-oauth2`
the NFM register endpoint is itself gated, so a consumer can't
self-register to get its first token. The runner sidesteps this with the
persistence mechanism: it registers the consumer against a sqlite NRF
*without* `-oauth2`, then restarts *with* `-oauth2` against the same DB
(the profile survives). The flow then runs against the `-oauth2` NRF:
no-auth NFM → 401, bstr → 200, token issued for the seeded consumer →
200, and the same NFM GET with `Authorization: Bearer {{ue.Params.token}}`
→ 200. NRF killed by PID.

---

## Run the concurrency suite (burst runner)

The NRF concurrency class (CONC-01..08, plan §2.16) has **two layers**:
Layer A is the Go `-race` suite in `d3x-core/nrf/concurrency_test.go`
(deterministic, race-detector-backed — see
[`concurrency-layer-a.md`](https://github.com/dflux-io/d3x-core/blob/main/docs/nrf/concurrency-layer-a.md));
Layer B is this black-box burst. `scripts/nrf-concurrency.sh` fires **N=100
truly-concurrent requests** at a fresh NRF per scenario and asserts the
cross-UE invariant that no single flow can see on its own.

```bash
cd /path/to/d3x-run
scripts/nrf-concurrency.sh
```

Expected tail:

```
[conc] PASS: CONC-01[memory] 100 distinct registrations
[conc] PASS: CONC-02[memory] same-UUID -> 1 profile
[conc] PASS: CONC-06[memory] distinct subscriptionIds
[conc] PASS: CONC-04[memory] register∥discover -> 100 NFs
[conc] PASS: CONC-01[sqlite] 100 distinct registrations
[conc] PASS: CONC-02[sqlite] same-UUID -> 1 profile
[conc] PASS: CONC-06[sqlite] distinct subscriptionIds
[conc] PASS: CONC-04[sqlite] register∥discover -> 100 NFs
[conc] passed: 8   failed: 0
```

The four flows are single-purpose and do **no cleanup** so post-burst
counts are meaningful:

| Flow | Burst | Cross-UE invariant (runner-verified) |
| --- | --- | --- |
| `conc_register` | 100 PUTs, distinct `{{$uuid}}` | NF list count == 100 (CONC-01) |
| `conc_register_same` | 100 PUTs, one shared UUID | NF list count == 1 (CONC-02) |
| `conc_subscribe` | 100 subscription POSTs | 100 **distinct** subscriptionIds (CONC-06) |
| `conc_register_discover` | register ∥ discover | NF list count == 100, no torn discover (CONC-04) |

Mechanics worth knowing:

- **Fresh NRF per scenario, not per backend.** Three of the four
  assertions are a post-burst population count; sharing one NRF across
  flows would sum those populations. Each scenario gets its own NRF (and
  its own sqlite WAL db).
- **Why the runner verifies, not the flow.** An runner flow is sequential and
  can't see other UEs' results, so "exactly N NFs exist" / "all
  subscriptionIds distinct" are checked by the runner — a `curl` of the NF
  list, or (subscriptions have no GET-collection endpoint) a scrape of the
  100 server-assigned IDs out of the burst's `-trace` output, asserting
  `sort -u` == 100.
- **CONC-07 (`sqlite`, no `database is locked`).** On the sqlite leg the
  runner greps the NRF log for lock errors and fails if any appear. This
  is the black-box proof of the `SetMaxOpenConns(1)` serialization in
  `d3x-core/cmd/nrf/main.go` — one writer connection over the WAL +
  `busy_timeout(5000)` path means N concurrent writers serialize cleanly
  instead of racing for the write lock.
- **Stale-binary trap.** A local `bin/d3x-run` predating a template
  feature fails to load the *whole* templates dir, which would silently
  skip these flows. So the runner **rebuilds runner from the current checkout
  first** (and rebuilds the NRF from `d3x-core` so the run includes
  the latest store fixes). Set `Runner_NO_BUILD=1` / `NRF_NO_BUILD=1` to use
  existing binaries as-is. NRF killed by PID, never `pkill -f`.

Env overrides: `CORE_REPO` (default `../d3x-core`), `NRF_BIN`,
`Runner_BIN`, `PORT` (default `8080`, must match `CONFIG`), `CONFIG`
(default `lab-nrf.yaml`), `TEMPLATES`, and `REPS` (burst size, default
`100`).

---

## Inspecting a run

Reports persist in runner's SQLite DB (`runner.db` in the working directory
by default):

```bash
./bin/d3x-run report list | head -5
./bin/d3x-run report list-suites | head -5
./bin/d3x-run report <execution-id>         # JSON to stdout
./bin/d3x-run report show-suite <suite-id>  # human summary
```

Add `-trace` to any `run-flow` / `run-suite` invocation to see TX/RX
hex dumps and per-FSM-transition logs in real time.

---

## Troubleshooting

| Symptom | Cause | Fix |
| --- | --- | --- |
| `error: no subscribers configured` | Missing `-s` flag | Add `-s config/subscribers.yaml` |
| `rest.send: no client binding for nf ""` | Flow missing `peer:` field on a `send` action | Every `send` needs `peer: nrf-peer` |
| Flow times out after 30s, RX=0 | NRF not reachable | `curl http://127.0.0.1:8080/health` first |
| Flow times out, RX=1 | Response event name wrong | Use `event: rest.<MessageName>_Answer` (note the `rest.` prefix) |
| `check failed status=<missing>` | Field path missing `rest.` prefix | Use `rest.status`, `rest.body.X`, `rest.headers.X` |
| OAuth: 500 `token signing not configured` | NRF started without `-jwt-key` | Add `-jwt-key /tmp/nrf-jwt.pem` |
| OAuth: 400 `invalid_client requester NF not registered` | Token requested for an unregistered nfInstanceId | Register an NF first (the OAuth flows do this) |
| `nrfInstanceId` missing in bootstrapping body | NRF started without `-nrf-id` | Add `-nrf-id "$(uuidgen)"` |
| `404 page not found` body in response when expecting JSON | Query params embedded in `url:` instead of `query:` | Move grant params to the `query:` field of `message_body` |

---

## Known engine workarounds

Two runner engine quirks surfaced during the original smoke-suite authoring;
the YAMLs here include defensive workarounds. Both are tracked as open
issues:

- **#273** — templated `expected: "{{ue.Params.X}}"` after an `extract`
  in the same `actions:` list doesn't substitute. Workaround:
  `nfm_register_201_location.yaml` uses `ue.Params.nfInstanceId
  not_empty` instead of `Location contains <extracted uuid>`.

- **#274** — `op: greater_than` against `expected: 0` fails even when
  the actual is unambiguously greater (`3600 > 0` returns false).
  Workaround: `oauth_token.yaml` uses `expires_in not_empty` instead
  of `expires_in > 0`.

When these land upstream the assertions can tighten back.

---

## Map of the plan

See [`docs/nrf/sanity-tests.md`](https://github.com/dflux-io/d3x-core/blob/main/docs/nrf/sanity-tests.md)
in d3x-core for the full test plan:

- §2.6 — schema notes (13 rules verified against runner)
- §2.7 — per-service suite catalogue
- §2.14 — persistence test class (NRF restart with sqlite)
- §2.15 — mTLS test class (Blocked SUT)
- §3 — TS 29.510 gap analysis
- §4 — per-service flow inventory + PR-by-PR delivery roadmap
