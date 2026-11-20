<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <img src="assets/icon.svg" alt="Prometheus logo" width="128">
  </picture>
</p>

<h1 align="center">aac-prometheus</h1>

<p align="center">Self-hosted deployment definition for Prometheus, an open-source monitoring system and time series database.</p>

<p align="center">
  <a href="https://github.com/prometheus/prometheus"><img alt="Upstream" src="https://img.shields.io/badge/upstream-prometheus%2Fprometheus-blue?logo=github"></a>
  <img alt="Image" src="https://img.shields.io/badge/image-prom%2Fprometheus%3Av3.14.0-E6522C?logo=docker&logoColor=white">
  <a href="https://github.com/prometheus/prometheus/blob/main/LICENSE"><img alt="License" src="https://img.shields.io/badge/license-Apache--2.0-green"></a>
</p>

## About

Prometheus collects and stores metrics as time series data, identified by metric name and
key/value labels. It scrapes HTTP endpoints at a configurable interval, evaluates alerting
and recording rules, and offers the PromQL query language for analysis. Prometheus runs as a
single self-contained server with local storage and does not depend on a distributed store.

Key features (as provided by upstream):

- Multi-dimensional time series data model with label-based querying (PromQL)
- Pull-based metric collection with service discovery and relabeling
- Alerting rules and recording rules, evaluated on the server
- Local time series storage with configurable retention
- Built-in web UI and HTTP API, including a lifecycle and admin API

## Upstream

- Project: <https://github.com/prometheus/prometheus>
- Website and documentation: <https://prometheus.io/docs/>
- Container image: `prom/prometheus:v3.14.0`
- Dependency: `prom/blackbox-exporter:v0.28.0` (deployed as `aac-prometheus-blackbox-exporter`)
- License: Apache-2.0 (upstream project)

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. For a
manual run, the external networks used for routing must exist first:

```bash
docker network create services-secured
docker network create services-exposed

# Provide the values listed under "Configuration" (CHANGE_ME) in .env
docker compose up -d
```

Prometheus reads its configuration from the `config` volume (`prometheus.yml`). Rule files
placed in the `rules` volume and file-based service discovery targets placed in the
`file_sd` volume are picked up without a restart.

## Configuration

Variables (defaults from `service.yml`; `CHANGE_ME` must be set per deployment):

| Variable | Default | Description |
|---|---|---|
| `DOCKER_TARGETS` | `CHANGE_ME` | Comma-separated list of Docker host scrape targets, used by the `docker` scrape job |
| `INTERNAL_DNS_ZONE` | `CHANGE_ME` | Internal DNS zone used for blackbox DNS probes |
| `AD_DNS_ZONE` | `CHANGE_ME` | Active Directory DNS zone used for blackbox DNS probes |
| `BLACKBOX_TIMEOUT` | `5s` | Probe timeout for the blackbox exporter modules |

No secrets are defined for this service.

Startup flags (from `service.yml`):

| Flag | Value | Description |
|---|---|---|
| `--config.file` | `/etc/prometheus/prometheus.yml` | Configuration file |
| `--storage.tsdb.path` | `/prometheus` | Time series storage path |
| `--storage.tsdb.retention.time` | `10d` | Retention by age |
| `--storage.tsdb.retention.size` | `5GB` | Retention by size |
| `--web.enable-lifecycle` | enabled | Allows configuration reload over HTTP |
| `--web.enable-admin-api` | enabled | Enables the TSDB admin API |

Ports:

| Port | Protocol | Purpose |
|---|---|---|
| `9090` | TCP | Web interface and HTTP API (`web`) |

Volumes:

| Mount | Description |
|---|---|
| `config` -> `/etc/prometheus` | Prometheus configuration file (`prometheus.yml`) |
| `data` -> `/prometheus` | Time series data storage |
| `rules` -> `/etc/prometheus/rules` | Alerting and recording rule files (`.yml`) |
| `file_sd` -> `/etc/prometheus/file_sd` | File-based service discovery targets (`.yml`) |

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, dependencies and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-prometheus>.

## Credits and license

Prometheus is developed by the Prometheus project and licensed under Apache-2.0. This
repository only contains the deployment definition for it and is not a fork of the
application. The icon is provided by the selfh.st icon collection; see
[`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md) for icon sources and licenses.
