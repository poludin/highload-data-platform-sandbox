# HighLoad Data Platform Sandbox

A 14-month `#BuildInPublic` R&D track and bare-metal/local sandbox dedicated to distributed systems, Linux kernel mechanics, Kubernetes internals, and stateful application reliability.

## 🎯 Mission Statement
Transitioning from standard infrastructure deployment to deep **Data SRE and Platform Engineering**. Rather than stopping at manifests, Helm charts, and baseline policies, this repository focuses on how clusters, kernels, networks, and stateful engines degrade under load—and how to prevent cascading failures. Every theoretical concept is validated through hands-on labs, fault injection, telemetry, and Go automation.

## 🏗 Sandbox Architecture
The environment combines a local Ubuntu control-plane/lab setup with a bare-metal **Proxmox VE** cluster running **Kubernetes / OKD**.
* **Storage:** Local Persistent Volumes on raw block devices, `StorageClass` binding modes (`WaitForFirstConsumer` vs `Immediate`), `Delete`/`Retain` policies, and `RollingUpdate` vs `OnDelete` quorum behavior.
* **Networking & Traffic:** Packet path analysis across XDP/TC/sockmap and `conntrack`, Cilium `kube-proxy` replacement, `NetworkPolicy` enforcement, CoreDNS (`ndots:5`, UDP conntrack races), and Ingress vs Gateway API (L4/L7, timeouts, retries, circuit breaking).
* **Kernel & Runtime:** `cgroups v2` (`working_set` vs `rss`, `page_cache`, `kmem`), `Guaranteed` QoS OOM edge cases, CFS CPU throttling and `p99` tail latency, graceful shutdown races (`SIGTERM`, `preStop`, `EndpointSlices`), and `bpftrace` profiling.
* **Governance & GitOps:** Least-privilege RBAC, Kyverno / admission guardrails, drift control, upgrade compatibility, and architectural trade-offs.
* **Automation:** Custom Go-based Kubernetes Operators (`controller-runtime`, Kubebuilder).

---

### 🛠 Tech Stack & Tools

**Systems Programming & Automation**  
![Go](https://img.shields.io/badge/go-%2300ADD8.svg?style=for-the-badge&logo=go&logoColor=white)
![Bash](https://img.shields.io/badge/Bash-4EAA25?style=for-the-badge&logo=gnubash&logoColor=white)
![Ansible](https://img.shields.io/badge/ansible-%231A1918.svg?style=for-the-badge&logo=ansible&logoColor=white)

**Infrastructure, Orchestration & Networking**  
![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white)
![OpenShift](https://img.shields.io/badge/OpenShift-EE0000?style=for-the-badge&logo=redhatopenshift&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Cilium](https://img.shields.io/badge/cilium-%23F6D15E.svg?style=for-the-badge&logo=cilium&logoColor=black)

**Distributed Databases & Messaging**  
![Apache Kafka](https://img.shields.io/badge/Apache%20Kafka-000?style=for-the-badge&logo=apachekafka&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/postgresql-%23316192.svg?style=for-the-badge&logo=postgresql&logoColor=white)
![ClickHouse](https://img.shields.io/badge/ClickHouse-FFCC01?style=for-the-badge&logo=clickhouse&logoColor=black)
![ScyllaDB](https://img.shields.io/badge/ScyllaDB-62B4CB?style=for-the-badge&logo=scylladb&logoColor=white)

**Observability, Telemetry & Policy**  
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/grafana-%23F46800.svg?style=for-the-badge&logo=grafana&logoColor=white)
![OpenTelemetry](https://img.shields.io/badge/OpenTelemetry-000000.svg?style=for-the-badge&logo=opentelemetry&logoColor=white)

---

## 🗺 R&D Roadmap & Modules

### [Phase 0: K8s Control Plane & Scheduler Internals](./00-k8s-control-plane-internals/)
* **Focus:** Control plane request pipeline, Raft/etcd stability, scheduling constraints, and disruption limits.
* **Key Labs:** Static pods, `etcd` snapshot/restore, `compact` and `defrag` under `fsync` latency, API Priority and Fairness (APF) under `PATCH/UPDATE` storms, `topologySpreadConstraints`, workqueue behavior, `PodDisruptionBudget` (PDB) boundaries, manual PKI rotation, and node network blackouts.

### [Phase 1: Messaging, Consensus & Disaster Recovery](./01-kafka-federation/) *(Active)*
* **Focus:** Apache Kafka physical storage, quorum resilience, safe stateful rollouts, and cross-cluster replication.
* **Key Labs:** Deploying KRaft without ZooKeeper via Strimzi on Local PVs, Page Cache & zero-copy (`sendfile`) I/O, ISR & broker `kill -9` chaos tests, `RollingUpdate` vs `OnDelete` impact on replication/quorum, and Active-Passive federation via MirrorMaker 2.

### [Phase 2: Concurrent Go Clients](./02-go-clients/)
* **Focus:** Resilient application-level streaming and pod lifecycle integration.
* **Key Labs:** Building high-throughput (1000+ RPS) Kafka producers/consumers in Go with worker pools, `context.Context` deadlines, exponential backoff retries, and graceful shutdowns aligned with `SIGTERM`, `preStop`, and `EndpointSlices`.

### [Phase 3: Linux Kernel, Network Tuning & eBPF](./03-network-tuning/)
* **Focus:** Kernel resource isolation, TCP/NIC throughput, eBPF data paths, DNS, and L4/L7 routing.
* **Key Labs:** `cgroups v2` (`working_set` vs `rss`/`page_cache`/`kmem`), `Guaranteed` OOM edge cases, CFS CPU throttling vs `p99` tail latency, TCP & NIC ring buffer tuning, Cilium `kube-proxy` replacement (XDP/TC/sockmap/conntrack), CoreDNS (`ndots:5`, UDP conntrack races), Gateway API routing, and `bpftrace` `fsync` profiling.

### [Phase 4: Heavyweight Distributed Databases](./04-distributed-dbs/)
* **Focus:** LSM-trees, columnar storage, thread-per-core execution, and stateful storage reliability.
* **Key Labs:**
  * **PostgreSQL:** WAL/MVCC, bloat and autovacuum tuning, HA failover, and connection pooling trade-offs.
  * **ClickHouse:** `MergeTree` parts, sparse indexes, background merges, and ClickHouse Keeper quorum.
  * **ScyllaDB:** CPU pinning (`Guaranteed` QoS), bypassing OS page cache (`O_DIRECT`), consistent hashing, and `p99` latency under load.

### [Phase 5: Kubernetes Operators, Governance & SRE Trade-offs](./05-k8s-operators/)
* **Focus:** Custom controllers, policy guardrails, GitOps lifecycle, and architectural decisions.
* **Key Labs:** Building CRDs and Go controllers via Kubebuilder (`Reconcile` loop, idempotency, finalizers), least-privilege RBAC, Kyverno / admission policies, GitOps drift and cluster/component upgrades, burn-rate SLO alerting, blameless post-mortems, and architecture trade-offs (single vs multi-cluster, operator vs externalized stateful tiers).

---
**Author:** Aleksandr Poludin  
**Connect:** [LinkedIn](https://www.linkedin.com/in/poludin/) | Follow weekly deep-dives and post-mortems under `#BuildInPublic`.
