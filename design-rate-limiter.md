# Designing a Rate Limiter
## 1. Clarify Functional Requirements
<p align="justify"> Before designing, it’s important to establish the scope and main goals of the rate limiter. Some guiding questions: </p>

### What are we protecting?

A single backend API?

Multiple endpoints?

A set of microservices, requiring a reusable component?

### How do we identify clients?

- IP-based identification (common and works even for unauthenticated users).

- UUIDs, API keys, or tokens (when clients are authenticated).

In many systems, a combination of strategies may be required.

### What level should the rate limit apply at?

- Global (all traffic combined).

- Per endpoint (different rules for different APIs).

- Per client/session.

## 2. Non-Functional Requirements
<p align="justify"> The rate limiter must meet performance, reliability, and usability goals. Important aspects: </p>

- Throughput & Scalability

Should support high request volumes without becoming a bottleneck.

Must scale horizontally (support distributed deployments).

Storage Requirements

Each client requires metadata (IP, timestamps, counters).

Rough estimate: ~132 bytes per client. For 1 billion users, that’s ~132 GB.

A fast in-memory KV store (e.g., Redis, Memcached) is suitable.

- Availability

Aim for high availability (e.g., five nines — 99.999%).

Define fallback behavior:

Fail-open: allow requests if the limiter fails (better uptime).

Fail-closed: block requests if the limiter fails (better abuse protection).

- Latency

Should add minimal overhead (ideally sub-millisecond).

Must not slow down critical API responses.

## 3. Algorithm Choices
<p align="justify"> Several algorithms can be used for rate limiting, each with trade-offs: </p>

- Fixed Window Counter

Easy to implement.

Can lead to burst traffic at window boundaries.

Sliding Window Log

Stores timestamps of requests in a log.

Precise but requires more memory.

- Sliding Window Counter

Balances accuracy and efficiency.

Less memory-intensive than logs.

- Token Bucket

Allows for short bursts while enforcing an average rate.

Commonly used in production systems.

- Leaky Bucket

Enforces a steady outflow rate.

Useful when smoothing traffic spikes.

## 4. Deployment Considerations
<p align="justify"> Where and how the rate limiter runs affects performance and reliability: </p>

- Client-side vs. Server-side

 Usually server-side (enforced at the API gateway or service).

In some cases, partially client-side (e.g., SDK-based throttling).

- Centralized vs. Distributed

Centralized: Easier to manage but may become a bottleneck.

Distributed: Scales better but requires coordination (e.g., Redis clusters, consistent hashing).

- Integration point

At the API Gateway (common for microservices).

Inside individual services (for fine-grained control).

## 5. Monitoring & Observability
<p align="justify"> A rate limiter is part of the security and reliability layer, so visibility is key: </p>

Metrics: requests allowed, requests blocked, latency overhead.

Alerts for abnormal traffic spikes.

Logging for forensic analysis (e.g., potential abuse patterns).