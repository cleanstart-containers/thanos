# 🔍 Thanos - Kubernetes Deployment Guide

Complete Kubernetes deployment guide for testing the CleanStart Thanos container. Thanos is a highly available Prometheus setup with long-term storage capabilities.

## 📁 Files

- `deployment.yaml` - Complete deployment manifest (Namespace, ServiceAccount, Deployment, Service)
- `README.md` - This documentation

## 🖼️ Image Details

**Image:** `cleanstart/thanos:latest-dev`

**Key Features:**
- **Binary Location:** `/usr/bin/thanos`
- **Command:** The deployment uses `command: ["/usr/bin/thanos"]` to run Thanos
- **User:** `clnstrt` (non-root, UID 1000)
- **Architecture:** `amd64`
- **OS:** `linux`
- **SSL Certificates:** Pre-configured at `/etc/ssl/certs/ca-certificates.crt`

## 🚀 Complete Deployment Steps

### Prerequisites

1. **Kubernetes cluster** (Kind, minikube, k3s, GKE, EKS, AKS, or any other)
2. **kubectl** installed and configured to access your cluster
3. **Docker** installed (for KIND image loading)

### Step 1: Verify Kubernetes Cluster

```bash
# Check cluster connectivity
kubectl cluster-info

# Verify nodes are ready
kubectl get nodes
```

### Step 2: Load Docker Image into KIND Cluster (KIND-specific)

**Important for KIND:** Since KIND uses its own Docker network, you need to load the Docker image into the cluster before deploying.

```bash
# Check existing KIND clusters
kind get clusters
```

**Note:** 
- For other Kubernetes clusters (minikube, GKE, EKS, etc.), ensure the image is available in your container registry or use `imagePullPolicy: Always` if the image is in a public registry.

### Step 3: Deploy Thanos

```bash
# Navigate to the deployment directory
cd containers/thanos/kubernetes

# Apply the deployment
kubectl apply -f deployment.yaml
```

**Expected Output:**
```
namespace/thanos-test created
serviceaccount/thanos created
deployment.apps/thanos created
service/thanos created
```

### Step 4: Verify Deployment

```bash
# Watch pod status (press Ctrl+C to exit)
kubectl get pods -n thanos-test -w
```

**Expected Output:**
```
NAME                      READY   STATUS    RESTARTS   AGE
thanos-xxxxxxxxxx-xxxxx   1/1     Running   0          30s
```

### Step 5: Check Pod Logs

```bash
# Follow logs in real-time
kubectl logs -n thanos-test -l app=thanos -f
```

**Expected Log Output (Success):**
```
level=info ts=2025-11-11T07:00:00.000Z caller=main.go:XXX msg="starting Thanos Query"
level=info ts=2025-11-11T07:00:00.000Z caller=query.go:XXX msg="starting query node"
level=info ts=2025-11-11T07:00:00.000Z caller=query.go:XXX component=query msg="Listening for query and metrics" address=0.0.0.0:10902
level=info ts=2025-11-11T07:00:00.000Z caller=query.go:XXX component=query msg="Listening for gRPC" address=0.0.0.0:10901
```

The Thanos Query component is now running and serving metrics on port 10902!

## 🧪 Testing

### Test 1: Verify Pod is Running

```bash
# Get pod name
POD_NAME=$(kubectl get pods -n thanos-test -l app=thanos -o jsonpath='{.items[0].metadata.name}')

# Check pod status
kubectl get pod $POD_NAME -n thanos-test

# View pod logs
kubectl logs $POD_NAME -n thanos-test

# Describe pod for detailed information
kubectl describe pod $POD_NAME -n thanos-test
```

### Test 2: Get Help/Version Information

```bash
# Get pod name
POD_NAME=$(kubectl get pods -n thanos-test -l app=thanos -o jsonpath='{.items[0].metadata.name}')

# Run version command
kubectl exec -n thanos-test $POD_NAME -- /usr/bin/thanos --version

# Run help command
kubectl exec -n thanos-test $POD_NAME -- /usr/bin/thanos --help
```

**Alternative: Test with Docker (local)**
```bash
# Run version command using Docker
docker run --rm --entrypoint="" cleanstart/thanos:latest-dev /usr/bin/thanos --version

# Check help
docker run --rm --entrypoint="" cleanstart/thanos:latest-dev /usr/bin/thanos --help
```

### Test 3: Test Thanos Query Component (Example)

```bash
# Get pod name
POD_NAME=$(kubectl get pods -n thanos-test -l app=thanos -o jsonpath='{.items[0].metadata.name}')

# Test query component (if you want to test query functionality)
# Note: This requires proper configuration with store endpoints
kubectl exec -n thanos-test $POD_NAME -- /usr/bin/thanos query --help
```

### Test 4: Verify Service

```bash
# Check service endpoints
kubectl get endpoints -n thanos-test thanos

# Check service details
kubectl get service -n thanos-test thanos -o yaml
```

### Test 5: Access Metrics via Port Forward

**✅ The deployment now runs Thanos Query component**, which serves metrics and a web UI on port 10902.

```bash
# Port-forward to access HTTP endpoint (metrics and UI)
kubectl port-forward -n thanos-test svc/thanos 10902:10902
```

**Access Metrics:**
```bash
# Get Prometheus metrics
curl http://localhost:10902/metrics
```

**Access Web UI:**
Open your browser and navigate to: `http://localhost:10902`

**Note:** The Thanos Query is running without store endpoints, so it won't have any Prometheus data to query. However, it will still serve:
- Its own metrics (Thanos Query metrics)
- The Thanos Query UI
- Health and readiness endpoints

## 🔧 Configuration Details

### Command Configuration

- **Binary Path:** `/usr/bin/thanos`
- **Deployment Command:** `command: ["/usr/bin/thanos"]`
- **Component:** `query` - Thanos Query component for querying metrics
- **Arguments:**
  - `--http-address=0.0.0.0:10902` - HTTP server for metrics and UI
  - `--grpc-address=0.0.0.0:10901` - gRPC server for query API
  - `--log.level=info` - Logging level
- **Note:** Running without `--store` flags means no data sources configured. The service will still start and serve its own metrics.

### Environment Variables

- `SSL_CERT_FILE=/etc/ssl/certs/ca-certificates.crt` - SSL certificate path

### Resource Limits

- **Requests:** CPU: 100m, Memory: 256Mi
- **Limits:** CPU: 500m, Memory: 512Mi

### Ports

- **10901:** gRPC endpoint (TCP)
- **10902:** HTTP endpoint (TCP)

## 🧹 Cleanup

```bash
# Delete all resources
kubectl delete -f deployment.yaml

# Or delete individually
kubectl delete deployment thanos -n thanos-test
kubectl delete service thanos -n thanos-test
kubectl delete serviceaccount thanos -n thanos-test
kubectl delete namespace thanos-test
```

## 📚 Use Cases

This Kubernetes deployment is perfect for:

- **Testing Thanos Image:** Verify the CleanStart Thanos container image functionality
- **Development Testing:** Test Thanos components in Kubernetes environment
- **CI/CD Integration:** Automated testing of Thanos image in pipelines
- **Component Validation:** Validate Thanos binary and basic functionality

## ✅ Verification Checklist

After deployment, verify:

- [ ] Pod is running (`kubectl get pods -n thanos-test`)
- [ ] Pod logs show "Listening for query and metrics" on port 10902
- [ ] Service endpoints are configured (`kubectl get endpoints -n thanos-test thanos`)
- [ ] Health probes are passing (check pod status)
- [ ] Port-forward works (`kubectl port-forward -n thanos-test svc/thanos 10902:10902`)
- [ ] Metrics endpoint accessible (`curl http://localhost:10902/metrics`)
- [ ] Web UI accessible (`http://localhost:10902`)
- [ ] No permission errors in logs
- [ ] Image loaded successfully (for KIND)

## 🔄 Next Steps

For production use, you would typically:

1. **Configure Thanos Components:** Update the deployment with specific Thanos component (query, store, sidecar, etc.)
2. **Add Storage Configuration:** Configure object storage (S3, GCS, Azure Blob, etc.)
3. **Set Up Service Discovery:** Configure service discovery for Prometheus instances
4. **Configure Retention:** Set up data retention policies
5. **Add Monitoring:** Set up monitoring for Thanos components themselves

## 📝 Notes

- **Current Setup:** The deployment runs Thanos Query component, which serves metrics and a web UI
- **No Data Sources:** Running without `--store` flags means no Prometheus data to query, but the service still serves its own metrics
- **Testing:** You can access metrics via port-forward and test the Thanos Query functionality
- **Production Use:** For production, add `--store` flags to connect to Thanos Store or Prometheus instances
- **Additional Components:** To test other components (store, sidecar, compact, etc.), modify the `args` in the deployment
- **Storage:** For long-term storage, configure object storage (S3, GCS, Azure Blob) and add appropriate flags

