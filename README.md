# Liquid Instruments

<!-- API-EVANGELIST-PROVENANCE:BEGIN -->
> ### About this repository
>
> **This is not our API.** This repository is an independent, third-party profile of a company's
> **publicly available** API surface, maintained by [API Evangelist](https://apievangelist.com).
> API Evangelist does not operate, host, resell, or support this company's APIs, and is not
> affiliated with or endorsed by the company unless stated on the profile.
>
> **Where the information came from.** Everything here is assembled from material a member of the
> public can reach with a browser and no credentials — the company's own website, developer portal
> and documentation, the specifications it publishes for public use (OpenAPI, AsyncAPI, JSON Schema,
> `apis.json`, `llms.txt` and similar), its public repositories, and its public status, pricing and
> changelog pages. **Nothing here is obtained by breaching a system, defeating an access control, or
> using credentials of any kind.**
>
> **The rating is an independent assessment.** The Kin Score and Agent Readiness rating are
> independently calculated scores of a company's *public* API artifacts, produced by API Evangelist
> against a published rubric. They are not certifications, endorsements, security assessments, or
> audits, and they score published artifacts — not the quality, safety, or security of the software.
>
> **Corrections, re-scores, and removal are free.** No partnership, contract, or purchase is
> required, and you do not need to justify the request.
>
> - **Something wrong?** Open an issue on this repository, or email
>   [info@apievangelist.com](mailto:info@apievangelist.com).
> - **Published something new?** Ask for a re-score and we will re-run the rating.
> - **Want the listing taken down?** Say so and we will honor it. The profile is reduced to your
>   company name, a factual description, and a link to your own site, and the company is recorded as
>   **unrated** — never scored zero for having asked.
>
> **Response times.** Acknowledgement within **one business day**; removal or restriction within
> **two business days**; corrections and re-scores within **five business days**.
>
> **Not from the company, and here with a question?** You are welcome here — we would rather be the
> front line and point you the right way than have a good report go nowhere. What this repository
> can answer is narrow, though, so it is worth knowing who you are actually looking for:
>
> - **A question about how the API works, an account, billing, or a bug in the service** — that is
>   the company's own support, not us. We profile this API; we do not operate it and cannot see
>   your account.
> - **A bug in an open-source project we only catalog** — file it on that project's own repository.
>   This has happened with a real and correct bug report that reached us instead of the people who
>   could fix it, which helped nobody.
> - **Anything about this listing itself** — the description, the tags, the rating, a missing or
>   wrong artifact — is ours. Open an issue here.
> - **Not sure, or something general about API Evangelist or APIs.io** — open an issue on the
>   [APIs.io Inbox](https://github.com/api-search/inbox) and we will route it.
>
> **This repository contains no software, and we will never ask you to download anything.** There is
> no build, release, installer, or binary here — only text and machine-readable API descriptions, so
> there is nothing here that can be "corrupt" or need "repairing". Any issue, comment, or email
> claiming otherwise and offering a download link is not from us and is hostile. Do not follow the
> link; it is a lure. Report it to GitHub and, if you like, tell us at
> [info@apievangelist.com](mailto:info@apievangelist.com) so we can take it down.
>
> **On a security or compliance team?** Email
> [info@apievangelist.com](mailto:info@apievangelist.com) with *security* in the subject line and
> you will get a person, not a form. We will tell you exactly which public URLs this profile was
> built from so your team can see the same surface we did, and we will take the listing down on
> request while you work through it.
>
> Full detail: **[Where this data comes from](https://apievangelist.com/about/where-our-data-comes-from)**
<!-- API-EVANGELIST-PROVENANCE:END -->

Liquid Instruments builds **Moku** — software-defined, FPGA-reconfigurable test-and-measurement
hardware (Moku:Go, Moku:Lab, Moku:Pro, Moku:Delta) that replaces a bench of traditional
instruments with one device. Headquartered in San Diego, California, with offices in Canberra
and Melbourne, Australia.

## The API surface

Two distinct surfaces were found and profiled (2026-08-04):

1. **Moku REST API** — a device-local HTTP control API. Each Moku serves its own API at
   `http://<device-ip>/api/<group>/<action>`; there is **no vendor-hosted base URL**. Every
   action is a `POST` of a JSON parameter object, authenticated with a `Moku-Client-Key`
   session key minted by `POST /api/moku/claim_ownership`. Responses always return HTTP 200
   with a `{success, code, messages, data}` envelope — failure is signalled in the body, not
   the status line. Wrapped by first-party Python (PyPI `moku`), MATLAB and LabVIEW clients
   plus the **MokuCLI** utility.
   Docs: https://apis.liquidinstruments.com/api/
2. **Liquid Instruments Identity** — a hosted OAuth 2.0 / OpenID Connect service at
   `auth.liquidinstruments.com` backing `mokucli login`, Moku Cloud Compile and licensing. It
   publishes a full OIDC discovery document and JWKS.

## What is in this repo

| Directory | Artifact |
|---|---|
| `authentication/` | Both auth surfaces — device client key + hosted OIDC |
| `scopes/` | OIDC scopes read verbatim from live discovery |
| `well-known/` | `/.well-known/` probe index + the OIDC discovery and JWKS documents |
| `conventions/` | Transport, RPC style, response envelope, versioning, timeouts, streaming |
| `errors/` | The error-code registry and HTTP failure modes |
| `data-model/` | 22 operation groups / 522 operations, derived from the first-party client |
| `lifecycle/` | Versioning, observed deprecations, support, status-page finding |
| `changelog/` | Recent API releases |
| `cli/` | The MokuCLI command surface |
| `packages/` | First-party client libraries and where they live |
| `conformance/` | Standards conformance, asserted only where observed |
| `security/` | Probed TLS/DNS posture |
| `llms/` | A generated `llms.txt` for this provider |

## Gaps worth closing (for Liquid Instruments)

Everything below is a factual observation from the public surface on 2026-08-04, not a criticism
of the hardware:

- **No machine-readable contract.** No OpenAPI, Swagger, GraphQL SDL or AsyncAPI on any host.
  The REST surface is real, uniform and well-suited to a spec — an OpenAPI generated from the
  same source that produces the reference pages would make the whole surface agent-usable.
- **Errors ride on HTTP 200.** Generic HTTP clients and agents cannot detect failure without
  parsing the envelope, and the error-code strings (`INVALID_PARAM`, `NO_BIT_STREAM`, …) are
  only enumerated inside the Python client source, not in the documentation.
- **`status.liquidinstruments.com` is broken.** The hostname is provisioned and pointed at
  UptimeRobot, but the TLS handshake fails, so no browser or agent can load it.
- **No `security.txt`, no vulnerability-disclosure page, no trust centre**, and no published
  compliance posture.
- **No deprecation policy** despite shipping real breaking changes (zero-indexed Logic Analyzer
  bits in 4.2.1, trigger-level defaults in 4.2.2, MokuCLI becoming mandatory in 4.0.1), and the
  changelog carries no dates.
- **No `llms.txt`, no MCP server, no A2A agent card.**

Secondary-market listing that surfaced this company:
https://forgeglobal.com/liquid-instruments_stock/
