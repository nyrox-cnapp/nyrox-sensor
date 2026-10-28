# Third-party components in the Nyrox runtime sensor image

| Component | Version | Licence | Source |
|---|---|---|---|
| Falco (engine, modern eBPF probe, default rules) | 0.45.0 | Apache License 2.0 (licenses/Apache-2.0.txt) | https://github.com/falcosecurity/falco |
| Falco container plugin | as shipped in Falco 0.45.0 | Apache License 2.0 | https://github.com/falcosecurity/plugins |

Copyright The Falco Authors. Falco is a Cloud Native Computing Foundation
project, used unmodified; Nyrox adds rules in /etc/falco/rules.d and its
configuration. The kernel-side eBPF probe comes from the Falco libs project
and carries the licence stated there (https://github.com/falcosecurity/libs).
