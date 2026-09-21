# Environment

## Confirmed components

| Component | Role in the lab |
|---|---|
| Ubuntu Server | Host operating system and defensive observation point |
| Docker | Runs the intentionally vulnerable training application |
| OWASP Juice Shop | Authorized local web-security training target |
| systemd services and timers | Runs health checks and scheduled reports |
| Fail2ban | Detects and responds to repeated failed SSH authentication attempts |
| Tailscale | Provides private remote connectivity |
| ntfy | Receives operational alerts; the topic is treated as sensitive and is not published |
| `docker inspect` | Reads container state for availability monitoring |
| `docker logs` | Reviews container output during authorized lab observation |
| `tcpdump` | Observes network metadata in the owned lab |
| `systemctl` | Verifies timers, services, and failed units |

## Sanitized architecture

```text
Private client / learning workstation
                |
        private lab connection
                |
        Ubuntu Server host
          |            |
      systemd       Docker
      monitors         |
          |       OWASP Juice Shop
          |
     notification service
```

Exact network ranges, hostnames, user accounts, hardware identifiers, and notification endpoints are intentionally omitted.

## Operational checks completed

- Scheduled monitoring timers were visible in `systemctl list-timers`.
- The Juice Shop container state was successfully included in a health report when the monitor had sufficient permission to query Docker.
- A final `systemctl --failed` check returned no failed systemd units at the time of validation.

## Known design trade-off

Querying Docker from an unprivileged service failed in the tested setup. The health-report service was therefore run with elevated privilege so it could read the container state. This works, but it expands the impact of a script error. A future improvement is to reduce privilege while retaining read-only visibility, for example through a narrowly scoped interface or a purpose-built metrics exporter.

