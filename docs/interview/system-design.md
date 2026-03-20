# System Design Interview Notes

> **TODO:** This document is a stub. Expand with detailed walkthroughs of common system design problems (URL shortener, Twitter, Netflix, etc.) and a structured approach framework.

## How to Approach a System Design Interview

1. **Clarify Requirements** — Functional and non-functional (scale, latency, availability)
2. **Estimate Scale** — Users, requests/sec, storage, bandwidth
3. **High-Level Design** — Sketch the major components
4. **Deep Dive** — Focus on the critical/interesting parts
5. **Identify Bottlenecks** — Single points of failure, hot spots
6. **Trade-offs** — Discuss what you chose and why

## Common System Design Topics

| Topic | Key Concepts |
|-------|-------------|
| Load Balancing | Round-robin, least connections, L4 vs L7 |
| Caching | Cache-aside, Redis, eviction policies |
| Databases | SQL vs NoSQL, sharding, replication |
| Message Queues | Kafka, RabbitMQ, async decoupling |
| CDN | Static asset delivery, edge caching |
| Rate Limiting | Token bucket, sliding window |
| API Design | REST, GraphQL, gRPC |
| Consistent Hashing | Distributed caching, minimizing remapping |

## Capacity Estimation (Quick Reference)

```
1 million requests/day  ≈ 12 req/sec
Read:Write ratio        - ask the interviewer
1 byte = 8 bits
1 KB = 1,000 bytes
1 MB = 1,000 KB
1 GB = 1,000 MB
1 TB = 1,000 GB
```

## Common Problems to Practice

- URL Shortener (e.g., bit.ly)
- Social Media Feed (e.g., Twitter timeline)
- Video Streaming (e.g., YouTube/Netflix)
- Chat Application (e.g., WhatsApp)
- Ride-sharing (e.g., Uber)
- Search Autocomplete
- Rate Limiter
- Distributed Cache

## Resources

- *System Design Interview* by Alex Xu
- [System Design Primer (GitHub)](https://github.com/donnemartin/system-design-primer)
- [Grokking the System Design Interview](https://www.educative.io/courses/grokking-the-system-design-interview)
