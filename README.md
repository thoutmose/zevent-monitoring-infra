# zevent-monitoring-infra

Version-controlled snapshot of the monitoring stack running on `srv-monitoring`
(`/opt/monitoring`): Prometheus, Grafana, Alertmanager, ntfy, Loki, blackbox_exporter.

Covers fleet-wide host monitoring (CPU/RAM/disk/load/ping for every inventory
host) plus zevent-project-specific dashboards and alerts: `srv-dev`/`srv-prod`
host metrics and PostgreSQL metrics for the `zevent`/`zevent-dev` databases
(running in Docker on `srv-db`, port 55432).

## Note on source of truth

Several files carry comments referencing `.j2` templates and an Ansible
`monitoring` role (e.g. `docker-compose.yml.j2`, `prometheus.yml.j2`) — this
repo is a snapshot of the **deployed, rendered output**, not that Ansible
source. If an Ansible control repo exists elsewhere, treat that as canonical
and mirror any changes made here back into it, or the next `ansible-playbook`
run will overwrite what's deployed.

## Secrets

Every credential in these files has been replaced with `CHANGEME`:
Grafana admin password, Alertmanager→ntfy-bridge basic auth, ntfy publish
auth, AdGuard exporter password. Fill in real values in a local, gitignored
copy before deploying — never commit real secrets here.

## Deploy

```bash
cd /opt/monitoring
docker compose up -d
```

Fill in the `CHANGEME` values first if deploying fresh. `postgres_exporter`
(host metrics + Postgres metrics for zevent) runs separately on srv-dev/
srv-prod/srv-db and srv-db respectively — see `postgres_exporter_queries.yml`
for the custom-metrics config it needs (`/etc/prometheus-postgres-exporter/queries.yaml`
on srv-db).
