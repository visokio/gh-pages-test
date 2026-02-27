---
title: Running Omniscope Fully In-Memory (Processing + Reporting)
---

[Home](index.md) | [Getting Started](getting-started.md) | [Linux Installation](linux-installation.md) | [Enable AI](enable-ai.md) | [Local AI Model](local-ai-model.md) | [Impala + Big Data](omniscope-impala-big-data.md) | [Concurrent Jobs](concurrent-workflow-jobs.md)

---

# Running Omniscope Fully In-Memory (Processing + Reporting)

Omniscope can now operate with both its processing and reporting layers running entirely in memory.

By enabling:
- the **In-Memory Workflow Execution Engine** (dataset building), and
- the **In-Memory Data Engine** (report querying),

Omniscope can execute workflows, publish data, and serve dashboards without relying on local disk I/O during normal operation.

In this configuration, RAM replaces the local runtime execution storage normally used during processing and reporting. It does **not** become a persistence layer — the data exists only while the Omniscope server process is running.

## What "fully in-memory" means

Omniscope has two runtime stages:

| Stage | What happens |
|-------|--------------|
| Processing | Workflow blocks execute and build datasets |
| Reporting | Dashboards query and filter published data |

Normally, during workflow execution Omniscope repeatedly materialises intermediate data to local disk and reads it back for downstream blocks. After publishing, the Data Engine loads the dataset into memory for report querying.

With both in-memory modes enabled, intermediate execution data is never materialised to local disk. The dataset is built in memory and then served directly from memory by the Data Engine.

Temporary files and intermediate execution tables are not written locally.

The server still requires a location for:
- project files
- configuration
- schedules
- optional logs

But the analytics runtime itself operates in RAM.

## Why you would use this

This setup is not just about speed — it enables new deployment models.

### Stateless analytics node

The Omniscope server can run on a machine that has CPU, RAM, and network access — but no meaningful local storage.

Examples:
- Kubernetes pods
- Ephemeral VMs
- Auto-scaling cloud workers
- Locked-down secure environments

The node can be created, execute or serve dashboards, and be destroyed without cleanup.

### Extremely fast pipelines

Disk I/O is frequently the largest delay in analytics workflows.

Fully in-memory operation removes:
- temporary table writes
- repeated read/write cycles
- storage contention

This is especially beneficial for frequent refresh dashboards, scheduled publishing, and transformation-heavy workflows.

### Better hardware utilisation

Modern machines typically have many CPU cores, large RAM, and fast network. Disk is often the slowest component. Fully in-memory mode allows CPU and RAM bandwidth to become the limiting factors instead of storage.

## Combining with live query data sources

A fully in-memory Omniscope server can also work with **live query connections** to external systems of record such as cloud data warehouses, SQL databases, and enterprise data platforms.

In this model:

**External database > queried live > processed in memory > served in memory**

Omniscope does not need to store a persistent local copy of the data.

This creates a hybrid architecture:
- The warehouse stores authoritative data
- Omniscope performs transformation, shaping, and visual analytics
- Dashboards remain highly responsive

The external **warehouse** remains the **system of record** and persistence layer, while **Omniscope** provides **transformation logic, shaping, and interactive analytics**. Omniscope becomes a compute and presentation layer rather than a data storage platform.

## Typical deployment patterns

### 1) Memory-resident reporting server

A dedicated machine:
- loads project from network storage
- publishes in memory
- serves dashboards in memory

No local disk required.

### 2) Container worker + reporting node

A containerised Omniscope instance:
- executes scheduled workflows
- publishes data
- serves users
- can be replaced at any time

### 3) Live warehouse analytics

Omniscope connects directly to a database and:
- performs shaping and enrichment
- caches working data in memory
- serves dashboards interactively

No staging database is required.

### 4) Burst compute processing

Create temporary machines only when needed:
- spin up node
- run workflows
- publish reports
- terminate node

## Memory planning

In this configuration, RAM becomes the primary resource.

Memory must support:
- workflow execution
- published dataset storage
- concurrent users
- query processing

| Workload | Recommended RAM |
|----------|-----------------|
| Small deployments | 32 GB |
| Typical server | 64–128 GB |
| Large or concurrent workloads | 128 GB+ |

Leave headroom for the operating system and background processes.

## Important operational considerations

### Swapping must be avoided

If the operating system begins paging memory to disk, performance drops sharply and may be worse than default operation.

Monitor:
- memory usage
- concurrent workflows
- user load

### Not a replacement for durable storage

Fully in-memory mode does not replace backups, persistent data storage, or project storage. It replaces only **temporary execution and runtime query storage**.

### Concurrency matters

Running multiple large workflows simultaneously can exhaust memory quickly. Consider:
- scheduled execution windows
- dedicated execution nodes
- limiting parallel jobs

### Memory persistence behaviour

When both the in-memory workflow execution engine and the in-memory Data Engine are enabled, datasets and report query data are stored in memory inside the Omniscope process.

This data is not written to local disk as part of normal operation.

The data remains available:
- after workflow execution completes
- while the project remains loaded
- while the Omniscope server process is running

The in-memory data is cleared when the Omniscope service is stopped or restarted.

#### Important consequence for reports

Because report data lives in memory, a server restart clears the published dataset currently held by the Data Engine.

After a restart:
- dashboards will reopen normally
- but data must be rebuilt or reloaded before reports show results again

This is expected behaviour in a fully in-memory configuration and should be considered when planning maintenance or failover procedures.

This is not data loss. The authoritative data remains in the project sources or external systems; the in-memory dataset is simply a runtime cache that must be rebuilt after restart.

### Restarts and failover

In a disk-based configuration, published data may remain available immediately after a restart.

In a fully in-memory configuration, the Omniscope node does not retain runtime data across restarts. Another node, or a workflow execution, must repopulate the datasets and reports data.

Typical approaches include:
- scheduled rebuild after startup
- a secondary node already running
- rebuilding from a warehouse or live data source

## When this architecture is ideal

Use a fully in-memory configuration when:
- storage is slow or restricted
- servers are ephemeral
- container orchestration is used
- workflows refresh frequently
- dashboard responsiveness is critical

## When not recommended

Avoid this setup when:
- datasets exceed available RAM
- memory usage is unpredictable
- long-running heavy ETL jobs dominate usage
- guaranteed intermediate persistence is required

## Horizontal scaling, failover and auto-scaling

Running Omniscope fully in memory enables a different deployment model compared to traditional BI servers.

Historically, analytics servers are **stateful** — they depend on local temporary files, accumulate runtime state, and replacing a node requires cleanup or warm-up time.

Because the in-memory workflow execution engine removes temporary execution files and the Data Engine can serve reports directly from memory, an Omniscope node no longer needs persistent local runtime state. In practice, this means an Omniscope server can be treated as a **replaceable compute node**.

### Horizontal scalability

Multiple Omniscope nodes can run simultaneously using the same project location and data sources. Instead of scaling vertically (a bigger server), capacity can be increased by:
- starting additional Omniscope instances
- distributing users across nodes
- dedicating some nodes to workflow execution and others to report serving

### Auto-scaling

Because a node does not rely on local temporary storage, new servers can be created only when needed:
- start nodes during business hours
- add nodes when user load increases
- create temporary nodes to run scheduled workflows
- automatically shut down idle nodes

This is particularly suitable for container orchestration platforms and cloud auto-scaling groups.

### Failover and resilience

If a node stops or is terminated:
- no temporary execution state is lost permanently
- another node can immediately replace it
- users can reconnect to a different server

Since the authoritative data remains in the source systems and project storage, nodes themselves become disposable compute capacity rather than critical infrastructure.

### Typical architecture

A common setup is:
- shared project storage (network share or repository)
- external database or warehouse as system of record
- multiple Omniscope nodes
- load balancer distributing users

Nodes can be added, removed, or replaced without changing the overall system.
