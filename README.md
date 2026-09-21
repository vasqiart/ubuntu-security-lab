# Ubuntu Security Lab

A defensive, self-hosted learning lab for observing Linux services, containers, logs, and network behavior in an environment I own and control.

This repository documents practical work with Ubuntu Server, Docker, OWASP Juice Shop, systemd, Fail2ban, Tailscale, `docker logs`, `tcpdump`, and health monitoring. The emphasis is on observability, detection, mitigation, and validation—not on attacking third-party systems.

## Purpose

The lab is used to learn a repeatable defensive workflow:

1. define an authorized scope;
2. observe normal service behavior;
3. introduce a safe, controlled failure;
4. collect host and container evidence;
5. detect the condition;
6. notify once without creating alert noise;
7. restore the service; and
8. validate recovery.

## Current documented work

### OWASP Juice Shop service monitoring

OWASP Juice Shop runs as a Docker container on an Ubuntu Server that I control. I built a small monitoring workflow that checks the container state on a schedule and sends a notification when the service is not running.

The workflow was validated by stopping the lab container, confirming one alert, confirming that repeated checks did not create duplicate alerts, restarting the container, and confirming that the alert state reset. A systemd timer runs the check automatically.

Read the full write-up: [Detecting and validating a stopped Juice Shop container](writeups/01-juice-shop-service-monitoring.md).

### Host health reporting

A separate scheduled health report records defensive operational context such as CPU, available memory, load average, disk utilization, Juice Shop state, Tailscale state, Fail2ban state, power state, battery level, connectivity, and uptime. Reports are scheduled three times per day with systemd.

## Repository map

- [Lab scope](docs/LAB_SCOPE.md)
- [Ethics and authorization](docs/ETHICS_AND_AUTHORIZATION.md)
- [Environment](docs/ENVIRONMENT.md)
- [Observability notes](docs/OBSERVABILITY.md)
- [Daybreak defensive use case](docs/DAYBREAK_USE_CASE.md)
- [Learning notes and roadmap](LEARNING_NOTES.md)
- [Security policy](SECURITY.md)

## Evidence standard

This portfolio separates completed work from planned work. A completed item is described only when it was observed or validated in the lab. Planned improvements are labeled as such. Raw logs, packet captures, notification topics, IP addresses, hostnames, account identifiers, and secrets are intentionally excluded from the public repository.

## Safety boundaries

- Only systems I own or environments for which I have explicit authorization are in scope.
- OWASP Juice Shop is intentionally vulnerable and is used only as a local training target.
- No third-party scanning, exploitation, credential testing, or persistence activity is documented here.
- Public examples are sanitized and omit sensitive infrastructure details.

## Status

This is an evolving learning portfolio. It does not claim professional certification, independent vulnerability discoveries, or production security coverage.

