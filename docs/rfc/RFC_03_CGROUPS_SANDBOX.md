# RFC-03: Linux cgroups v2 Quota Architecture

- Isolation: CPU CFS quota (100000 50000), 256MB RAM cap, PID ceiling 64
- Egress: Strict netns isolation preventing unauthorized outbound sockets