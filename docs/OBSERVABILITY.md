# Observability Notes

## Three evidence layers

The lab uses three complementary views. None is sufficient by itself.

### 1. Service state

`docker inspect` and `systemctl` answer questions such as:

- Is the container running?
- Is a timer scheduled?
- Did a one-shot service fail?

These checks are useful for availability, but a running process is not proof that an application is healthy or secure.

### 2. Application and container output

`docker logs` can show output emitted by the container. It is useful for troubleshooting and for learning what the application records.

Important limitation: absence of a useful security event in container output does not prove that the event did not occur. Logging depends on what the application emits, how Docker is configured, and whether upstream components record additional context.

### 3. Network observation

`tcpdump` can show packet and connection metadata visible at the selected interface. It can help answer questions such as:

- Did traffic reach the host?
- Which protocol and ports were involved?
- Was there a connection attempt or response?

Important limitation: encrypted application content is not readable merely because packets are captured. A packet capture also does not explain application intent by itself. Host, container, and application evidence should be correlated.

## Safe public-evidence policy

Raw logs and packet captures are not published by default because they can expose IP addresses, hostnames, tokens, cookies, request content, or other personal data. Public write-ups use summarized and sanitized observations instead.

