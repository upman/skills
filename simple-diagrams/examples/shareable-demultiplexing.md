# Shareable Demultiplexing Example

Use this example when a diagram needs to show one producer sending a shared stream into a component that splits it into central, tenant, regional, or otherwise scoped destinations. The original pattern came from a Langfuse trace demultiplexing diagram: application traces go to a shared collector, central export receives every trace, tenant export receives only marked traces, feedback scores bypass the collector, and unrelated application telemetry stays on its existing path.

## What To Preserve

- Keep configuration, runtime producers, routing attributes, shared fanout infrastructure, and destinations as separate boxes.
- Show the always-on central path separately from filtered tenant or regional paths.
- Show routing markers near the component that creates them.
- Show direct API paths, such as feedback scores, separately from collector/exporter paths.
- Put unrelated telemetry in a visually lighter lane so it does not compete with the main demultiplexing story.
- Use destination keys such as `eu`, `us`, or `jp` precisely; do not call them tenant ids unless they are tenant ids.
- Label whether metadata is transport context, trace attributes, stored state, or outbound headers.

## Block Diagram Sketch

```mermaid
flowchart LR
  settings["Tenant settings\nregion + public key\nsecret key reference"]
  pods["Application API pods\nruntime credential resolver"]
  attrs["Trusted trace attributes\nexport enabled\ndestination = eu | us | jp"]
  collector["Shared fanout collector\nOTLP/HTTP traces only"]
  pipelines["Central pipeline\nTenant routing pipeline"]
  central["Central destination\nreceives all traces"]
  tenant["Tenant destinations\neu | us | jp\nmarked traces only"]
  scores["Scores API\nPOST /api/public/scores"]
  appotel["Application OTel SDK\nHTTP, DB, cache, model calls"]
  generic["Existing OTel collector"]
  backend["Observability backend"]

  settings <--> pods
  pods --> attrs
  pods -->|"Langfuse OTLP/HTTP trace export"| collector
  collector --> pipelines
  pipelines -->|"central trace"| central
  pipelines -->|"tenant trace when marker + destination match"| tenant
  pods -.->|feedback scores REST API| scores
  appotel -.->|generic OTLP/HTTP| generic
  generic -.->|service telemetry| backend
```

## Sequence Sketch

```mermaid
sequenceDiagram
  participant Settings as Tenant settings
  participant App as Application API pod
  participant Collector as Shared fanout collector
  participant Central as Central destination
  participant Tenant as Tenant destination
  participant Scores as Scores API
  participant OTel as Existing OTel path

  Settings->>App: validated region + credential reference
  App->>Collector: OTLP trace with tenant export marker
  Collector->>Central: central trace export
  Collector->>Tenant: tenant trace export when destination matches
  App-->>Scores: feedback score REST request
  App-->>OTel: generic application telemetry
```

## Label Notes

- Say `tenant destination` or `destination key` for values like `eu`, `us`, and `jp`.
- Say `collector transport metadata` when a header is available to the collector but is not persisted as a trace field.
- Say `trusted trace attributes` only when the application actually creates the marker used for routing.
- Say `delete routing attrs before export` if the collector strips internal routing markers before forwarding.
