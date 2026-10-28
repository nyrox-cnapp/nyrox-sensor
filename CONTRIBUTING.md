# Contributing

Issues and pull requests are welcome: new or better rules, chart fixes,
documentation.

- **Rules** go in `images/runtime-sensor/rules/`. Say in the pull request
  what the rule catches, and how you checked that it does not fire on
  ordinary workloads.
- **Chart**: `helm lint charts/nyrox-sensor` and
  `helm template charts/nyrox-sensor` must pass (CI runs both).
- **Upgrading Falco**: change `FALCO_VERSION` / `FALCOSIDEKICK_VERSION`, the
  Falco chart dependency (`helm dependency update charts/nyrox-sensor`), and
  the versions in each `THIRD_PARTY_NOTICES.md`, in one pull request.

## Sign-off

Every commit must be signed off ([Developer Certificate of
Origin](https://developercertificate.org/)), stating that you wrote it or
have the right to contribute it under the Apache License 2.0:

```sh
git commit -s -m "…"
```
