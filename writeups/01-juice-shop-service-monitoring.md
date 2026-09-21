# Detecting and Validating a Stopped Juice Shop Container

## Summary

This exercise built a small defensive monitoring workflow around a Docker-hosted OWASP Juice Shop instance on an owned Ubuntu Server. The goal was not vulnerability exploitation. The goal was to detect a service failure, avoid repeated alert noise, recover the service, and verify that monitoring returned to its normal state.

## Scope and authorization

- Target: a local OWASP Juice Shop container
- Host: a privately controlled Ubuntu Server
- Test type: controlled container stop/start
- External systems: none
- Sensitive values published: none

## Objective

Detect when the Juice Shop container is not running and send one actionable alert. Do not send the same alert every minute while the condition remains unchanged. After recovery, reset the state so a future independent failure can generate a new alert.

## Observation design

The monitor followed this state machine:

```text
Read container state
        |
        v
Is the container running? ---- yes ----> clear prior alert state
        |
        no
        |
Was an alert already sent? --- yes ----> exit without duplicate alert
        |
        no
        |
Send alert and record alert state
```

The container state was read with Docker. A systemd timer executed the check every minute. A small local state file recorded whether an alert had already been sent for the current outage.

## Validation procedure

1. Confirm the Juice Shop container is running.
2. Run the monitor and confirm no outage notification is sent.
3. Stop only the owned Juice Shop container.
4. Run or wait for the scheduled monitor.
5. Confirm that a single outage notification is received.
6. Allow another monitoring interval to pass.
7. Confirm that no duplicate outage notification is generated.
8. Restart the Juice Shop container.
9. Run or wait for the monitor and confirm the recorded alert state is cleared.
10. Check the systemd timer and failed-unit state.

## Observed results

- The stopped container was detected.
- One notification was received for the outage.
- Repeated checks during the same outage did not create duplicate notifications.
- Restarting the container returned the service to the running state.
- The alert state reset after recovery.
- The monitoring timer was enabled and waiting for its next execution.
- A later system-wide check showed no failed systemd units.

## Detection value

The workflow provides a useful availability signal and demonstrates several defensive engineering principles:

- explicit state collection;
- separation of normal and abnormal conditions;
- suppression of duplicate alerts;
- recovery-aware state reset;
- scheduled execution;
- post-change validation.

## Limitations

The monitor checks container process state. It does not prove that:

- the HTTP application is responding correctly;
- every Juice Shop dependency is healthy;
- application-level security events are being logged;
- traffic is legitimate;
- the container image is free of known vulnerabilities.

`docker logs` and packet observation can add context, but neither replaces an application-level health check. A stronger next version should validate an expected HTTP response and record latency, then correlate that result with container and host state.

## Mitigation and hardening considerations

- Keep the intentionally vulnerable application isolated from production or personal services.
- Avoid publishing the lab directly to the public internet.
- Restrict who can query or control Docker.
- Treat notification endpoints and topics as sensitive.
- Rotate or limit logs so monitoring cannot exhaust disk space.
- Keep the Ubuntu host and Docker runtime patched.
- Use least privilege for monitoring components where practical.

## Permission finding

In the tested setup, an unprivileged health-report service could not query Docker, so the Juice Shop state appeared as unknown. Running that report with elevated privilege allowed the state to be read correctly.

This solved the immediate visibility problem but introduced a least-privilege concern. The elevated service should remain small, static, and carefully reviewed. Replacing broad privilege with narrowly scoped read-only monitoring remains an improvement item.

## Next validation

Planned improvements are clearly separated from completed work:

- add an HTTP health check in addition to container-state monitoring;
- capture sanitized timestamps for detection and recovery latency;
- document log retention and rotation;
- reduce monitoring privilege;
- correlate service state, container output, and network metadata;
- preserve sanitized screenshots or excerpts as public evidence.

## What I learned

A useful alert is more than a condition check. It needs state, noise control, recovery handling, scheduling, and validation. I also learned that observability permissions are part of the security design: granting a monitor enough access to see a service can itself increase risk.

