# Ingest API

What the forwarder sends to Nyrox. Any client that speaks it can report to
Nyrox; the forwarder is Falcosidekick's webhook output, unchanged.

## Request

```
POST /api/ingest/runtime/{connector_id}
Authorization: Bearer <ingest token>
Content-Type: application/json
```

`X-Nyrox-Token: <ingest token>` is accepted instead of `Authorization`.

The body is one Falco event as Falco writes it in JSON, or an array of at
most 100 of them. The whole body is at most 512 KiB.

```json
{
  "uuid": "…",
  "time": "2026-10-26T09:12:03.123456789Z",
  "rule": "Nyrox contact cloud instance metadata from container",
  "priority": "Critical",
  "source": "syscall",
  "hostname": "node-1",
  "output": "Connection to the instance metadata service from a container (…)",
  "tags": ["container", "network", "mitre_credential_access", "T1552.005"],
  "output_fields": {
    "k8s.ns.name": "shop",
    "k8s.pod.name": "api-7d9f-x2k",
    "proc.name": "curl",
    "proc.pname": "sh",
    "container.image.repository": "registry.example.com/shop/api"
  }
}
```

`rule` is required. Every other field is optional: a field that is missing,
or that Falco wrote as `<NA>`, is stored as unknown, never guessed.

The chart adds these to `output_fields` for every syscall alert, whatever
the rule's own output names: `proc.name`, `proc.exepath`, `proc.cmdline`,
`proc.pid`, `proc.pname`, `proc.pcmdline`, `proc.aname[2..4]`, `user.name`,
`user.uid`, `container.id`, `container.name`, `container.image.repository`,
`container.image.tag`, `container.image.digest`, `k8s.ns.name`,
`k8s.pod.name`.

The cluster is read from the first of `nyrox_cluster`, `nyrox.cluster`,
`cluster`, `k8s.cluster.name` (Falcosidekick's `customfields`).

### Priority to severity

| Falco priority | Nyrox severity |
|---|---|
| Emergency, Alert, Critical | critical |
| Error | high |
| Warning | medium |
| Notice | low |
| Informational, Debug | info |

## Responses

| Status | When |
|---|---|
| 202 | `{"accepted": n, "rejected": n, "findings": ["…"]}`; rejected events had no `rule` |
| 400 | The body is not JSON |
| 401 | Missing or wrong token, unknown connector, or a connector that is not a runtime sensor. All four answer the same, so the API does not reveal which connector ids exist |
| 403 | The connector is disabled, or the tenant is suspended |
| 413 | Body over 512 KiB, or more than 100 events |
| 422 | No event in the body had a `rule` |

## Token

Shown, and rotated, in Nyrox > Connectors > Nyrox runtime sensor > Sensor
setup. Rotating it stops the old token at once: update the
`nyrox-sensor-webhook` Secret and restart the forwarder.
