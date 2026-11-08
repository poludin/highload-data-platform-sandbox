# HighLoad Data Platform Sandbox

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
![ClickHouse](https://img.shields.io/badge/ClickHouse-FFCC01?style=for-the-badge&logo=clickhouse&logoColor=black)
![Apache Kafka](https://img.shields.io/badge/Apache%20Kafka-000?style=for-the-badge&logo=apachekafka&logoColor=white)
![PostgreSQL](https://img.shields.io/badge/postgresql-%23316192.svg?style=for-the-badge&logo=postgresql&logoColor=white)
![ScyllaDB](https://img.shields.io/badge/ScyllaDB-62B4CB?style=for-the-badge&logo=scylladb&logoColor=white)

**Observability & Telemetry**  
![Prometheus](https://img.shields.io/badge/Prometheus-E6522C?style=for-the-badge&logo=prometheus&logoColor=white)
![Grafana](https://img.shields.io/badge/grafana-%23F46800.svg?style=for-the-badge&logo=grafana&logoColor=white)
![OpenTelemetry](https://img.shields.io/badge/OpenTelemetry-000000.svg?style=for-the-badge&logo=opentelemetry&logoColor=white)

A comprehensive 14-month R&D track and bare-metal sandbox dedicated to distributed systems, kernel-level networking, and stateful application reliability. 

## 🎯 Mission Statement
Transitioning from standard infrastructure deployment to deep **Data SRE and Platform Engineering**. This repository serves as an open engineering diary and portfolio, documenting the process of breaking, observing, and automating enterprise-grade stateful systems on Kubernetes. The methodology is strictly "lab-first": every theoretical concept is immediately tested by pulling the plug on a running cluster.

## 🏗 Sandbox Architecture
The environment is built on a bare-metal **Proxmox VE** cluster running **Kubernetes/OKD**. 
* **Storage:** Local Persistent Volumes (Raw block devices, direct I/O, no network storage abstractions).
* **Networking:** Moving from standard CNI (iptables/kube-proxy) to eBPF-based routing (Cilium).
* **Automation:** Custom Go-based Kubernetes Operators (`controller-runtime`, Kubebuilder).

## 🗺 R&D Roadmap & Modules

### [Phase 0: K8s Control Plane Internals](./00-k8s-control-plane-internals/)
* **Focus:** Dismantling K8s magic. 
* **Key Labs:** `etcd` snapshot/restore surgery, manual PKI certificate rotation, static pod assassination, and simulating network blackouts on worker nodes.

### [Phase 1: Messaging, Consensus & Disaster Recovery](./01-kafka-federation/) *(In Progress)*
* **Focus:** Apache Kafka physical storage and cross-datacenter replication.
* **Key Labs:** Deploying KRaft without ZooKeeper via Strimzi, forcing split-brain scenarios, Page Cache & zero-copy (`sendfile`) utilization, and Active-Passive federation using MirrorMaker 2.

### [Phase 2: Concurrent Go Clients](./02-go-clients/)
* **Focus:** Resilient application-level integration.
* **Key Labs:** Building high-throughput (1000+ RPS) Kafka producers and consumers in Go. Implementing worker pools, exponential backoff retries, and graceful shutdowns during broker failures.

### [Phase 3: Linux Network Tuning & eBPF](./03-network-tuning/)
* **Focus:** Squeezing maximum throughput out of the Linux kernel.
* **Key Labs:** TCP Window Scaling, NIC RX/TX ring buffers tuning, replacing `kube-proxy` with Cilium (XDP/eBPF), and writing `bpftrace` scripts to profile disk I/O (`fsync` latency) in real-time.

### [Phase 4: Heavyweight Distributed Databases](./04-distributed-dbs/)
* **Focus:** LSM-trees, Columnar storage, and Thread-per-core architectures.
* **Key Labs:** 
  * **PostgreSQL:** Autovacuum tuning and HA failover validation (CloudNativePG / Patroni).
  * **ClickHouse:** Merges, sparse indexes, and ClickHouse Keeper quorum.
  * **ScyllaDB:** CPU pinning (Guaranteed QoS), bypassing the OS cache (`O_DIRECT`), and consistent hashing.

### [Phase 5: Kubernetes Operators & SRE Patterns](./05-k8s-operators/)
* **Focus:** Automating infrastructure state.
* **Key Labs:** Building Custom Resource Definitions (CRDs) via Kubebuilder. Writing a Go controller with a reconciliation loop to automate database provisioning and user management, enforcing strict idempotency and safe failovers.

---
**Author:** Aleksandr Poludin  
**Connect:** [LinkedIn](https://www.linkedin.com/in/poludin/) | Follow my weekly deep-dives and post-mortems under the `#BuildInPublic` hashtag.