# Collecting Railway data (read-only)

Working dir: use a temp dir (`mktemp -d`), run python with `-I`.

## Totals

```sh
railway whoami --json                      # workspace id
railway usage --workspace <id> --period previous --json   # line items + total
railway usage --workspace <id> --period 2026-08 --json    # earlier period (YYYY-MM)
railway usage projects --workspace <id> --period previous --json   # per-project $ (has `share`)
railway usage limit status --workspace <id> --json        # spend limits (null = none set)
```

Billing period resets on the 13th ~10:08 UTC. Use exact start/end from `billingPeriod` in the JSON.

## Per-service usage (the part the CLI doesn't give you)

`railway list --json` gives projects, environments, service ids/names. Then per project:

```graphql
# q_usage.graphql
query($projectId: String!, $start: DateTime!, $end: DateTime!) {
  usage(projectId: $projectId, startDate: $start, endDate: $end,
        measurements: [MEMORY_USAGE_GB, CPU_USAGE, DISK_USAGE_GB, NETWORK_TX_GB, NETWORK_RX_GB, BACKUP_USAGE_GB, EPHEMERAL_DISK_USAGE_GB],
        groupBy: [SERVICE_ID, ENVIRONMENT_ID]) {
    measurement value tags { serviceId environmentId pluginId volumeId }
  }
}
```

```sh
railway api -f q_usage.graphql --var 'projectId="<id>"' --raw-var start=<ISO> --raw-var end=<ISO> --compact
```

`--var` strings must be JSON-quoted; `--raw-var` for dates. Transient failures happen: retry once. Units are GB-minutes / vCPU-minutes. Avg GB = GB-min ÷ minutes in period (43,200 for 30 days).

### Rates (verify against the receipt; they change)

| item | rate |
|---|---|
| memory | $0.000231481 / GB-min (~$10 / GB-month) |
| CPU | $0.000462963 / vCPU-min |
| disk (volume + backup) | $0.0000034722 / GB-min |
| egress | $0.05 / GB (TX only) |

Sum per project and compare to `railway usage projects`. If memory is off by a constant ratio, scale it so the total matches Railway's line item (we used ~0.9865). Services with no `serviceId` and no name = deleted service remnants.

## Config per service

```graphql
query($id: String!) { environment(id: $id) { id name config(decryptVariables: false) } }
```

Read from `config.services.<id>`: `deploy.sleepApplication`, `deploy.limitOverride.containers`, `deploy.numReplicas` / `multiRegionConfig`, `deploy.cronSchedule`, `source.image|repo|rootDirectory`, `networking.serviceDomains|customDomains|tcpProxies`. Never decrypt variables.

```graphql
query($s:String!,$e:String!){ serviceInstance(serviceId:$s, environmentId:$e){ serviceName latestDeployment{ status createdAt } } }
```

`status: SLEEPING` vs `SUCCESS` tells you who sleeps.

## Live / time-series (sanity, not billing)

```sh
railway metrics -p <projectId> -e production -a --since 1h --memory --cpu --json     # all services
railway metrics -p <projectId> -e production -s <service> --since 14d --memory --raw --json   # series (no --all with --raw)
```

Series are coarse for long windows (4h buckets hide bursts; 24h gives ~4-min buckets). Report both baseline and peak vs the limit.

## Qdrant (if present) — read-only

```sh
railway run -p <projectId> -e production -s Qdrant -- sh -c 'curl -s -H "api-key: $QDRANT__SERVICE__API_KEY" https://$RAILWAY_PUBLIC_DOMAIN/collections'
```

Per collection: `points_count`, `indexed_vectors_count`, `segments_count`, `config.params.vectors` (size, distance, on_disk), `quantization_config`, `hnsw_config`. Rough size: points × dim × 4 B. `/metrics` `memory_resident_bytes` vs Railway's number shows how much is page cache. Fix levers: scalar int8 quantization (`PATCH /collections/{name}`), `on_disk` vectors, lower dimension, container memory limit. Code lives in `packages/sinks/src/qdrant.ts` (ragstudio).

## Repos (gh)

```sh
gh api repos/bilbyai/<repo>/contents/<path> -q .content | base64 -d
gh search code --owner bilbyai 'WORKER_CONCURRENCY'
```

Look for: concurrency defaults, Dockerfile/start commands, always-on workers that could sleep/merge, queue retention, DB pool sizes.
