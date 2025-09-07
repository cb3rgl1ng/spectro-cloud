# Debug with Kubectl Commands

Kubectl is a Command Line Interface (CLI) tool for interacting with Kubernetes clusters (K8s) via the Kubernetes API server. For more information on kubectl, see [Command line tool (kubectl)](https://kubernetes.io/docs/reference/kubectl/). For kubectl in Palette, see [Kubectl in the SpectroCloud docs](https://docs.spectrocloud.com/clusters/cluster-management/palette-webctl/).

Kubectl issues commands against Kubernetes clusters. Here are useful kubectl commands for debugging, in order of operation:

| Command | Description | Usage |
| ----------- | ----------- | ----------- |
| `get pods` | Lists all available pods and their status. See [kubectl get](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_get/).<br/>**Note:** Make sure to specify the `namespace`.| `kubectl get pods --namespace` |
| `logs` | Retrieves logs of a specific pod to review logs or debug a container. See [kubectl logs](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_logs/).<br>**Note:** If a pod has multiple containers, you must use `-c <container>`. | `kubectl logs [-f] [-p] (POD NAME) [-c CONTAINER]` |
| `exec` | Executes a command in a container to debug a container from the inside or to explore the the envirnoment of the container itself. See [kubectl exec](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_exec/). | `kubectl exec (POD NAME) [-c CONTAINER] [flags] -- COMMAND [args...]` |
| `debug` | Creates a clone of a pod (inactive debugging container) that does not terminate if an error is experienced inside the container. See [kubectl debug](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_debug/). | `kubectl debug (POD NAME) [ -- COMMAND [args...] ]` |

## Example Debugging Session

### get pods

Start by listing pods to see what is active or failing.

**Command:**
```shell
kubectl get pods --namespace
```

**Response:**
```shell
NAME                               READY   STATUS             RESTARTS   AGE
web-frontend-7d9d6f47cf-2qhts      1/1     Running            0          2d
api-backend-6899b6c87d-kqjxl       0/1     CrashLoopBackOff   5          10m
db-postgres-0                      1/1     Running            0          2d
```

The `CrashLoopBackOff` status indicates that `api-backend` is failing.

### logs

Next, look at the logs to see why `api-backend` is failing.

**Command:**
```shell
kubectl logs api-backend-6899b6c87d-kqjxl --namespace
```

**Response:**
```shell
Error: failed to connect to database at db-postgres:5432
Caused by: timeout after 5s
```

The error indicates a database connection issue.

### exec

Next, open a shell inside the pod to investigate and test.

**Command:**
```shell
kubectl exec -it api-backend-6899b6c87d-kqjxl --namespace -- /bin/sh
```

**Response:**
```shell
# env | grep DB
DB_HOST=db-postgres
DB_PORT=5432
DB_USER=admin
# nc -zv db-postgres 5432
Connection to db-postgres 5432 port [tcp/postgresql] succeeded!
```

Since environment values are set and database connectivity works, the issue requires further investigation.

### debug

Finally, if more tools are needed, initiate a temporary debug container.

**Command:**
```shell
kubectl debug -it api-backend-6899b6c87d-kqjxl --namespace --image=busybox --target=api-backend
```

**Response:**
```shell
Creating debugging pod api-backend-6899b6c87d-kqjxl-debug ...
If you don't see a command prompt, try pressing enter.
# ping db-postgres
PING db-postgres (10.244.1.20): 56 data bytes
64 bytes from 10.244.1.20: seq=0 ttl=64 time=0.234 ms
```

## References
- [Command line tool (kubectl)](https://kubernetes.io/docs/reference/kubectl/)
- [Kubectl Commands - Getting Started](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-)
- [kubectl get](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_get/)
- [kubectl logs](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_logs/)
- [kubectl exec](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_exec/)
- [kubectl debug](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_debug/)
- [Kubectl in the SpectroCloud docs](https://docs.spectrocloud.com/clusters/cluster-management/palette-webctl/)
- [What is Kubernetes](https://kubernetes.io/docs/concepts/overview/)
