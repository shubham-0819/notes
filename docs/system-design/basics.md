# System Design Basics

> **TODO:** This document is a stub. Expand with deeper coverage of each topic, diagrams, trade-off analysis, and real-world examples.

## Key Goals of System Design

- **Scalability** — Handle growing load (users, data, traffic)
- **Availability** — System stays up despite failures
- **Reliability** — System behaves correctly over time
- **Maintainability** — Easy to change and operate
- **Performance** — Low latency, high throughput

## Horizontal vs Vertical Scaling

| | Vertical Scaling | Horizontal Scaling |
|-|------------------|--------------------|
| What | Bigger machine (more CPU/RAM) | More machines |
| Limit | Hardware ceiling | Practically unlimited |
| Cost | Expensive at scale | Commodity hardware |
| Complexity | Simple | Requires load balancing, distributed coordination |

## Common Building Blocks

| Component | Purpose |
|-----------|---------|
| Load Balancer | Distribute traffic across servers |
| CDN | Serve static assets close to users |
| Cache (Redis/Memcached) | Reduce DB load, speed up reads |
| Message Queue (Kafka, RabbitMQ) | Async processing, decouple services |
| Database | Persistent storage |
| Object Storage (S3) | Store files, images, backups |
| API Gateway | Single entry point, routing, auth |

## CAP Theorem

A distributed system can only guarantee **two** of the following three:

- **Consistency (C)** — Every read returns the latest write
- **Availability (A)** — Every request gets a response
- **Partition Tolerance (P)** — System works despite network failures

In practice, P is unavoidable, so you choose between **CP** or **AP**.

## Caching

- **Where to cache:** Client, CDN, Application layer, Database layer
- **Strategies:** Cache-aside, Write-through, Write-back, Read-through
- **Eviction policies:** LRU, LFU, TTL

## Database Scaling

- **Indexing** — Speed up reads
- **Read replicas** — Offload reads from primary
- **Sharding** — Split data across multiple DBs by a shard key
- **Denormalization** — Duplicate data for faster reads

## API Design Basics

- **REST** — Stateless, resource-based, uses HTTP verbs
- **GraphQL** — Query exactly the data you need
- **gRPC** — Binary protocol, efficient for internal services

## References

- [System Design Primer](https://github.com/donnemartin/system-design-primer)
- [Grokking the System Design Interview](https://www.educative.io/courses/grokking-the-system-design-interview)
