# Nyrox runtime sensor

The in-cluster part of [Nyrox](https://nyrox.app): one pod per node that
watches the system calls of every container with eBPF, and a forwarder that
sends its alerts to Nyrox, where they are linked to the inventory and
investigated.

Everything that runs inside your cluster is in this repository, so you can
read what you install before you install it.

| Path | What it is |
|---|---|
| `images/runtime-sensor` | [Falco](https://falco.org) 0.45.0, unmodified, plus the Nyrox rules in `rules/` |
| `images/sensor-forwarder` | [Falcosidekick](https://github.com/falcosecurity/falcosidekick) 2.32.0, unmodified |
| `charts/nyrox-sensor` | Helm chart: the Falco chart (vendored, so nothing is fetched at install) configured for Nyrox |
| `docs/ingest-api.md` | What the forwarder sends to Nyrox, and what Nyrox answers |

## What it does, and what it does not

- Runs Falco's modern eBPF probe: no kernel module. Needs a kernel 5.8 or
  later with BTF (e.g. 5.15, 6.8).
- Runs privileged on every node, control-plane nodes included. The
  least-privileged capability set is not used because not every node
  runtime grants CAP_BPF/CAP_PERFMON.
- Downloads nothing when it starts: rules and the container plugin are in
  the image, and falcoctl is off. It installs where there is no route to the
  internet.
- Sends alerts of priority `notice` and above, as Falco's own JSON, to one
  address: your Nyrox ingest URL, over HTTPS with certificate checking on.
  Nothing else leaves the cluster.
- Takes no action on workloads: response actions and the k8s-metacollector
  are off.

## Install

Copy the address and token from Nyrox > Connectors > Nyrox runtime sensor >
Sensor setup, keep them in a Secret (never in values), then install:

```sh
kubectl create namespace nyrox-sensor
kubectl -n nyrox-sensor create secret generic nyrox-sensor-webhook \
  --from-literal=WEBHOOK_ADDRESS='https://<nyrox address>/api/ingest/runtime/<connector id>' \
  --from-literal=WEBHOOK_CUSTOMHEADERS='Authorization:Bearer <token>'

helm install nyrox-sensor oci://ghcr.io/nyrox-cnapp/charts/nyrox-sensor \
  --version 1.0.0 -n nyrox-sensor
```

From a clone: `helm install nyrox-sensor ./charts/nyrox-sensor -n nyrox-sensor`.

If the same pod names exist in several clusters, name the cluster as Nyrox's
Kubernetes connector knows it:

```sh
--set falco.falcosidekick.config.customfields='nyrox_cluster:<cluster name>'
```

## Rules

Falco's default rules, then `images/runtime-sensor/rules/nyrox-rules.yaml`.
The Nyrox rules are ordinary Falco rules and work without Nyrox. Images that
call the cloud metadata service on purpose go in the
`nyrox_metadata_allowed_images` list.

## Verifying the images

Images are signed with [cosign](https://github.com/sigstore/cosign) keyless
signing from this repository's release workflow:

```sh
cosign verify ghcr.io/nyrox-cnapp/nyrox-runtime-sensor:v1.0.0 \
  --certificate-identity-regexp 'https://github.com/nyrox-cnapp/nyrox-sensor/.github/workflows/release.yml@refs/tags/v.*' \
  --certificate-oidc-issuer https://token.actions.githubusercontent.com
```

## Licence

Apache License 2.0, see [LICENSE](LICENSE) and [NOTICE](NOTICE). Falco and
Falcosidekick are by The Falco Authors (CNCF), also Apache-2.0; each image
lists its components in `THIRD_PARTY_NOTICES.md`.

The rest of Nyrox (the platform the sensor reports to) is proprietary and
not in this repository.
