# Changelog

All notable changes to this deployment definition are documented in this file.
The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Changed

- Upstream image tracks Prometheus v3.14.0 as defined in `service.yml`.

## [0.0.1]

### Added

- Initial App-as-Code service definition for Prometheus with a blackbox exporter dependency.
- Web interface and HTTP API on port 9090 with Traefik routing and automatic DNS.
- Persistent volumes for configuration, time series data, rule files and file-based service discovery.
- README with configuration reference and project icon.
