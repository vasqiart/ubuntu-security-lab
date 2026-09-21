# Learning Notes and Roadmap

## Completed and validated

- Operated OWASP Juice Shop in Docker on an owned Ubuntu Server.
- Checked container state for availability monitoring.
- Created scheduled systemd checks for the lab environment.
- Tested a controlled Juice Shop container stop and restart.
- Confirmed one alert for the outage and suppression of repeated alerts.
- Confirmed alert-state reset after service recovery.
- Built a scheduled health report covering host and service state.
- Verified scheduled timers with `systemctl`.
- Verified that no systemd units were in a failed state at the final check.
- Used container logs and packet observation as learning tools while recognizing their evidentiary limits.

## Lessons

- A running container is not the same as a healthy application.
- Alerts need deduplication and recovery logic to remain useful.
- Monitoring access is also a privilege boundary.
- Logs, service state, and network metadata answer different questions.
- Public security evidence must be sanitized before publication.
- Documentation should distinguish observation, inference, and future work.

## Planned next work

- Add an HTTP-level health check for Juice Shop.
- Record sanitized detection and recovery timings.
- Review log retention and rotation.
- Reduce Docker-monitoring privilege.
- Add a reproducible evidence template for future write-ups.
- Study secure configuration changes and validate them before and after application.
- Contribute a small defensive documentation or hardening improvement to an open-source project.

## Evidence template for future entries

Each future write-up should contain:

1. authorized scope;
2. objective;
3. environment;
4. baseline;
5. controlled test;
6. observations;
7. detection logic;
8. mitigation;
9. validation;
10. limitations;
11. sanitized evidence;
12. lessons learned.

