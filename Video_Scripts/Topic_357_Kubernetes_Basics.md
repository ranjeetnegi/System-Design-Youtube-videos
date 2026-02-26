# What is Kubernetes? (Container Orchestration)

## Video Length: ~4-5 minutes | Level: Intermediate

---

## The Hook (20-30 seconds)

You have 10 containers. Easy. `docker run` on a few machines. You have 100 containers across 50 machines. One crashes. Which one? Where? You scale up for traffic. Which machines have capacity? You deploy an update. Do you restart all at once and take down the system? Managing containers at scale is chaos without an orchestrator. **Kubernetes** is that orchestrator. It knows where every container runs. It restarts crashed ones. It scales them up and down. It rolls out updates without downtime. Let me show you how.

---

## The Story

**Kubernetes** (K8s) is a **container orchestrator**. You tell it what you want: "Run 5 replicas of my API. Keep them healthy. Expose them on port 80." Kubernetes figures out *where* to run them, *how* to keep them running, and *how* to route traffic. You declare the desired state. Kubernetes reconciles reality to match. That's **declarative config**. You don't say "start this container on that machine." You say "I want 5 of these." K8s does the rest.

The core building blocks: A **cluster** is a set of machines (nodes) running Kubernetes. A **node** is a single machine—a VM or bare metal—that runs your containers. A **pod** is the smallest deployable unit: one or more containers that share a network and storage. Usually one container per pod. The pod is the unit of scheduling. Kubernetes schedules pods onto nodes. A **Deployment** is a higher-level object: it manages replicas of a pod template. "I want 5 replicas of this pod." The Deployment creates a **ReplicaSet**, which ensures 5 pods always exist. One dies? ReplicaSet creates another. **Self-healing**. A **Service** gives pods a stable network identity. Pods come and go. IPs change. The Service has a stable name (e.g., `api-service`) and load-balances traffic to healthy pods. Clients talk to the Service. They don't care which pod answers.

**Rolling updates:** You change the image in your Deployment. Kubernetes gradually replaces old pods with new ones. One at a time, or in batches. Old and new run side by side during the rollout. No big-bang restart. **Rolling update**. If something goes wrong, you roll back. Same mechanism. Declarative. YAML files define everything. `kubectl apply -f deployment.yaml`. That's the workflow.

---

## Another Way to See It

Think of Kubernetes as a conductor of an orchestra. Each musician (container) plays their part. The conductor (Kubernetes) doesn't play instruments. They ensure everyone is in the right seat, starts on time, and if someone drops out, a substitute steps in. The conductor has the score (your YAML). The orchestra (cluster) follows it. You change the score? The conductor adjusts. Kubernetes is the conductor. Your containers are the musicians.

---

## Connecting to Software

- **Cluster** = set of nodes running Kubernetes. The control plane + worker nodes.
- **Node** = a machine (VM or physical) that runs pods. Has a container runtime (containerd, CRI-O).
- **Pod** = smallest unit. One or more containers sharing network/storage. Usually 1:1.
- **Deployment** = manages replicas. Declares "I want N pods." Handles rolling updates, rollbacks.
- **Service** = stable network endpoint. Load-balances to pods. Clients use Service name, not pod IPs.
- **kubectl** = CLI to talk to Kubernetes. `kubectl get pods`, `kubectl apply -f file.yaml`, `kubectl logs`, `kubectl describe`.
- **YAML** = declarative config. You define desired state. Kubernetes reconciles.
- **Real tools:** **minikube** (local K8s), **kind** (K8s in Docker), **EKS** (AWS), **GKE** (Google), **AKS** (Azure).

---

## Let's Walk Through the Diagram

```
    KUBERNETES CLUSTER
    ==================

    ┌─────────────────────────────────────────────────────────────────┐
    │  CONTROL PLANE (API Server, Scheduler, etcd)                     │
    └─────────────────────────────────────────────────────────────────┘
                                    │
         ┌──────────────────────────┼──────────────────────────┐
         ▼                          ▼                          ▼
    ┌─────────┐                ┌─────────┐                ┌─────────┐
    │ Node 1  │                │ Node 2  │                │ Node 3  │
    │ ┌─────┐ │                │ ┌─────┐ │                │ ┌─────┐ │
    │ │Pod A│ │                │ │Pod B│ │                │ │Pod C│ │
    │ │Pod A│ │   Service      │ │Pod B│ │   Service      │ │Pod B│ │
    │ └─────┘ │ ◄──────────────┼─└─────┘ │ ◄──────────────┼─└─────┘ │
    └─────────┘   (load bal)   └─────────┘   (load bal)   └─────────┘
         │                          │                          │
    Deployment says "5 replicas"     ReplicaSet ensures 5 exist  Self-healing
```

---

## Real-World Examples (2-3)

**1. Google.** Kubernetes came from **Borg**—Google's internal container orchestrator. They ran billions of containers. Borg was proprietary. Kubernetes is the open-source version. Google donated it to the CNCF. Today, Google Cloud runs Kubernetes at massive scale. GKE (Google Kubernetes Engine) is their managed offering. The same patterns that run Google Search run your startup's API.

**2. Spotify.** Migrated from VMs to Kubernetes. Thousands of microservices. Rolling updates without downtime. Self-healing when services crash. They run on GKE. Deploy hundreds of times per day. Kubernetes handles the complexity.

**3. Airbnb.** Uses Kubernetes for their data pipeline, ML workloads, and core services. Multi-region. Thousands of pods. Declarative config. Teams own their deployments. Kubernetes abstracts the infrastructure. Developers focus on code.

---

## Let's Think Together

**Question:** You have a Deployment with 3 replicas. One pod crashes. What happens?

**Pause. Think about it...**

**Answer:** The **ReplicaSet** (managed by the Deployment) notices the pod is gone. The desired state is 3. The actual state is 2. Kubernetes schedules a new pod to replace it. Within seconds, you're back to 3. That's self-healing. You didn't do anything. The controller loop (ReplicaSet) continuously reconciles. Crash, restart, scale—Kubernetes handles it.

---

## What Could Go Wrong? (Mini Disaster Story)

A team deployed their app to Kubernetes. They set **resource requests** but not **resource limits**. A memory leak in one pod. The pod kept consuming RAM. The node ran out of memory. The Linux OOM killer killed processes—including other pods on the same node. Cascading failures. Multiple services down. The fix? Set both requests and limits. `limits` cap how much a pod can use. A leaky pod gets killed and restarted—it doesn't take down the node. Lesson: Kubernetes gives you power. Resource management (requests, limits) is not optional. Set them. Test them. Your future self will thank you.

---

## Surprising Truth / Fun Fact

**Kubernetes** means "helmsman" or "pilot" in Greek—the person who steers a ship. The logo is a ship's wheel. The idea: Kubernetes steers your containers. It navigates the sea of nodes. Fitting. Also: "K8s" = K + 8 letters + s. A common abbreviation. Pronounce it "kates" or "k-eights." Both work.

---

## Quick Recap (5 bullets)

- **Kubernetes** = container orchestrator. Declarative: you say what you want, K8s makes it happen.
- **Pod** = smallest unit. **Deployment** = manages replicas. **Service** = stable endpoint, load-balances.
- **Self-healing** = crashed pods get replaced automatically. ReplicaSet reconciles.
- **Rolling updates** = deploy new version gradually. No big-bang. Rollback if needed.
- **kubectl** = CLI. YAML = config. `kubectl apply -f file.yaml`. That's the workflow.

---

## One-Liner to Remember

> **Kubernetes is the conductor for your containers. Declare what you want. It schedules, scales, heals, and rolls out—automatically.**

---

## Next Video

Kubernetes can run your containers. But how does it know when to add more? When traffic spikes, you need **auto-scaling**—the system adds instances automatically and removes them when traffic drops. No manual intervention. That's next.
