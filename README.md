## 🧪 Kubernetes Probes Lab: Readiness vs. Liveness

### ✅ Goal

This lab will show you how Kubernetes reacts to failures in an NGINX container using:

* **Liveness Probe**: Simulate a NGINX crash by deleting the `index.html` file.
* **Readiness Probe**: Use `/tmp/ready` to simulate unavailability.

### 🔍 Notes

* **Readiness** is used to **determine traffic routing**.
* **Liveness** is used to **restart crashed** or **stucked containers**.

---

## 🔧 Step 1: Create the Deployment

```bash
cd k8s-probes-lab
kubectl apply -f nginx-probes.yaml
```

---

## 🧪 Step 2: Test Behavior

### 3.1 Watch the pod status

```bash
kubectl get pods --selector=app=nginx-probe -w
```

You should see that the pod is running but it is not in a ready state. This is because the readiness probe is failing.

```bash
kubectl describe pod <pod-name>
```

"... Readiness probe failed: cat: /tmp/ready: No such file or directory"

Let's fix this.

---

### 3.2 Fix the Readiness Probe

```bash
kubectl exec <pod-name> -- touch /tmp/ready

kubectl get pod --selector=app=nginx-probe -w

kubectl run busybox --image busybox -- sleep 3600

kubectl exec busybox -- wget nginx-probe-svc.default.svc
```

The pod should now be in a ready state.

### 3.3 Simulate **Liveness Failure**

Now that our pod is ready and serving traffic, let's simulate a crash.

```bash
kubectl exec <pod-name> -- rm /usr/share/nginx/html/index.html
```

💡 *Effect: Container is restarted by Kubernetes.*

---

## 🧹 Cleanup

```bash
kubectl delete -f nginx-probes.yaml
```

