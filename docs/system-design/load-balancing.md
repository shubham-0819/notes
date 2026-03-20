# Load Balancing

> **TODO:** This document is a stub. Expand with real-world configurations, cloud-specific load balancers (AWS ALB/NLB), health checks, and case studies.

## What is Load Balancing?

Load balancing is the process of distributing incoming network traffic across multiple servers to ensure no single server is overwhelmed, improving availability and reliability.

## Why It Matters

- Prevents single points of failure
- Increases throughput and concurrency
- Enables horizontal scaling
- Enables zero-downtime deployments (rolling updates)

## Load Balancing Algorithms

| Algorithm | Description | Best For |
|-----------|-------------|----------|
| **Round Robin** | Requests distributed sequentially | Uniform servers |
| **Least Connections** | Routes to server with fewest active connections | Long-lived connections |
| **IP Hash** | Routes based on client IP (sticky sessions) | Session affinity |
| **Weighted Round Robin** | Servers with higher weight get more requests | Heterogeneous servers |
| **Random** | Randomly picks a server | Simple, uniform load |

## Types of Load Balancers

### Layer 4 (Transport Layer)
- Operates at TCP/UDP level
- Faster, no content inspection
- Example: AWS NLB

### Layer 7 (Application Layer)
- Operates at HTTP/HTTPS level
- Can route based on URL, headers, cookies
- Example: AWS ALB, Nginx, HAProxy

## Health Checks

Load balancers periodically probe backend servers to detect failures:
- **Active checks** — LB sends requests to a health endpoint (`/health`)
- **Passive checks** — LB monitors real traffic for errors

Unhealthy servers are removed from the pool until they recover.

## Session Persistence (Sticky Sessions)

Some applications require a user to always hit the same server (e.g., in-memory session state). This is achieved via:
- Cookie-based affinity
- IP hash

**Better alternative:** Use shared session storage (Redis) so any server can handle any request.

## Common Tools

- **Nginx** — High-performance reverse proxy and load balancer
- **HAProxy** — Reliable, high-performance TCP/HTTP load balancer
- **AWS ALB/NLB** — Managed load balancers on AWS
- **Kubernetes Ingress** — L7 load balancing inside K8s clusters

## References

- [Nginx Load Balancing Docs](https://nginx.org/en/docs/http/load_balancing.html)
- [HAProxy Docs](https://www.haproxy.org/#docs)
