# Stack monitoring (Docker)

Architecture : **Prometheus** (métriques), **Loki** (logs), **Tempo** (traces), **Promtail** (logs Docker), **OpenTelemetry Collector** (réception OTLP), **Grafana** (visualisation).

## Démarrage

```bash
cd monitoring
docker compose up -d
```

| Service            | URL / port hôte |
|-------------------|-----------------|
| Grafana           | http://localhost:3000 (admin / admin) |
| Prometheus        | http://localhost:9090 |
| Loki              | http://localhost:3100 |
| Tempo (API HTTP)  | http://localhost:3200 |
| OTEL Collector    | OTLP gRPC `localhost:4317`, HTTP `localhost:4318` |

## Envoyer télémétrie depuis une application

- **Traces / métriques / logs OTLP** : `OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4317` (gRPC) ou `http://localhost:4318` (HTTP), selon votre SDK.
- Le collector envoie les traces vers **Tempo**, les métriques vers l’exposition Prometheus (`:8889`, scrapée par Prometheus), les logs vers **Loki**.

## Logs des conteneurs

**Promtail** lit les journaux Docker via `/var/run/docker.sock` et les pousse vers Loki. Les labels `container`, `image`, etc. sont ajoutés automatiquement.

## Scraper vos propres métriques

Éditez `prometheus/prometheus.yml` et ajoutez un `job_name` avec les cibles HTTP qui exposent `/metrics` (ou un `metrics_path` adapté).

## Dashboards Grafana

Les fichiers JSON placés dans `grafana/dashboards/` sont chargés automatiquement (voir `grafana/provisioning/dashboards/dashboards.yml`).

## Données persistantes

Volumes Docker : `prometheus_data`, `loki_data`, `tempo_data`, `grafana_data`.
