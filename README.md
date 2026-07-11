# Kubernetes

Great, let's get into it properly.

Since you already know Docker and had an intro to Kubernetes, I'll assume you know *why* Kubernetes exists (orchestrating containers at scale, restarting failed ones, load balancing, scaling up/down). Let's build on that with the core building blocks you'll actually use day to day.

## The Kubernetes Mental Model

Kubernetes manages **containers running on a cluster** through a set of objects you define in YAML. You describe the *desired state*, and Kubernetes constantly works to make reality match it.

### 1. **Pod** — the smallest deployable unit
- A Pod wraps one (or occasionally more) containers.
- Pods are **ephemeral** — they can die and get replaced with a new IP. You rarely create Pods directly.

### 2. **Deployment** — manages Pods for you
- You tell a Deployment: "I want 3 replicas of this container image running."
- It creates Pods, watches them, and replaces any that crash.
- This is how you do rolling updates and scaling.

### 3. **Service** — stable networking for Pods
- Since Pods come and go with changing IPs, a Service gives them a stable name/IP/DNS entry.
- Types: `ClusterIP` (internal only), `NodePort` (exposes on each node's IP), `LoadBalancer` (cloud-provided external IP).

### 4. **ConfigMap & Secret** — configuration and sensitive data
- ConfigMap: non-sensitive config (env vars, config files).
- Secret: sensitive data (passwords, tokens) — base64 encoded, not encrypted by default.

### 5. **Namespace** — logical separation
- Lets you split a cluster into virtual clusters (e.g., `dev`, `staging`, `prod`).

---

Here's a quick visual of how these pieces connect:Traffic comes in through the **Service**, which forwards it to whichever Pods are healthy. The **Deployment** sits above the Pods, constantly checking "do I have 3 running?" and spinning up replacements if one dies. **ConfigMap** and **Secret** inject configuration and credentials into the Pods without baking them into the container image.

### What's next, practically

The best way to actually learn this is to spin up a local cluster and deploy something. Two easy options:
- **minikube** — single-node cluster, great for learning, has a dashboard
- **kind** (Kubernetes in Docker) — runs nodes as Docker containers, faster startup

Once you have one running, the typical first exercise is:
```bash
kubectl create deployment nginx --image=nginx --replicas=3
kubectl expose deployment nginx --port=80 --type=NodePort
kubectl get pods
kubectl get svc
```
