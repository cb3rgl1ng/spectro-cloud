# Debug Workloads in Kubernetes

Knowing how to debug workloads is essential for managing a Kubernetes environment. This reference reviews key kubectl commands used for inspecting and troubleshooting pods and containers.

**Note:** To understand the difference between pods and containers, see [Pods](https://kubernetes.io/docs/concepts/workloads/pods/) and [Containers](https://kubernetes.io/docs/concepts/containers/).

## kubectl: The Kubernetes CLI

Kubectl is a Command Line Interface (CLI) tool for interacting with Kubernetes clusters (K8s). Communicating with the Kubernetes API server, kubectl retrieves information, issues commands, and inspects cluster resources. 

For more information on kubectl, see [Command line tool (kubectl)](https://kubernetes.io/docs/reference/kubectl/). <br/>For kubectl in Palette, see [Kubectl in the Spectro Cloud docs](https://docs.spectrocloud.com/clusters/cluster-management/palette-webctl/).

Here are useful kubectl commands for troubleshooting and debugging deployed pods and containers, in order of operation:

| Command | Description | Reference | Notes |
| ----------- | ----------- | ----------- | ----------- |
| `get pods` | Lists all available pods and their status | [kubectl get](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_get/) | Make sure to specify the `namespace`. |
| `logs` | Retrieves logs of a specific pod to review or debug a container | [kubectl logs](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_logs/) | If a pod has multiple containers, use `-c <container>`. |
| `exec` | Executes a command in a container to debug a container from the inside or to explore the environment of the container itself | [kubectl exec](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_exec/) | Not all containers include the common shells `bin/bash` or `bin/sh`. |
| `debug` | Creates a clone of a pod (inactive debugging container) that does not terminate if an error is experienced inside the container | [kubectl debug](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_debug/) | When you finish working in the debugging pod, delete it. |

For more information on kubectl commands, see [Kubectl Commands](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands/).

## Example Debugging Session

The following example workflow uses kubectl commands to investigate failing pods in Kubernetes:

 ![Kubectl Debugging Workflow](./img/debugWorkflow.jpg)

### `kubectl get pods`

Start by listing pods in a specified namespace to see what is active or failing.

**Command:**
```shell
kubectl get pods -n demo
```

**Response:**
```shell
NAME          READY   STATUS             RESTARTS      AGE
debug-demo    0/1     CrashLoopBackOff   1 (13s ago)   18s
working-pod   1/1     Running            0             18s
```

Inspect columns such as `STATUS`, `RESTARTS`, and `AGE` to determine if pods are crashing, pending, or running as expected. In the example, the `CrashLoopBackOff` status indicates that `debug-demo` is failing.

For more information, see [kubectl get](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_get/).

### `kubectl logs`

Next, look at the logs to see why `debug-demo` is failing.

**Note:** As long as a terminated pod hasn't been garbage collected, you can still retrieve logs for it.

**Command:**
```shell
kubectl logs debug-demo -n demo
```

**Response:**
```shell
ERROR: Failed to connect to database at db.example.com:5432
```

In the example, the error indicates a database connection issue.

For more information, see [kubectl logs](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_logs/).

### `kubectl exec`

Next, open a shell inside the pod to investigate and test. From here, you can inspect the container's runtime environment, check file contents, or run diagnostics.

**IMPORTANT:** Be cautious when using this in production environments. Depending on the commands issued, `exec` can modify the container state. Always follow change management protocols.

**Command:**
```shell
kubectl exec -it debug-demo -n demo -- /bin/sh
```

**Response:**
```shell
# env
KUBERNETES_SERVICE_PORT=443
KUBERNETES_PORT=tcp://10.96.0.1:443
HOSTNAME=working-demo
HOME=/root
PKG_RELEASE=1~bookworm
DYNPKG_RELEASE=1~bookworm
TERM=xterm
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
NGINX_VERSION=1.29.1
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
KUBERNETES_PORT_443_TCP_PORT=443
NJS_VERSION=0.9.1
KUBERNETES_PORT_443_TCP_PROTO=tcp
NJS_RELEASE=1~bookworm
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_SERVICE_HOST=10.96.0.1
PWD=/
# nc -zv db-postgres 5432
Connection to db-postgres 5432 port [tcp/postgresql] succeeded!
# exit
```

In the example, the environment values are set and database connectivity works, so the issue requires further investigation.

The example runs `env` and `nc`, but `cat`, `top`, `df`, and `ps` are other useful commands under `exec`.

**Note:** Make sure to run `exit` to terminate the command.

For more information, see [kubectl exec](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_exec/).

### `kubectl debug`

Finally, if additional tools are needed, debug in place within a temporary container inside the pod. This temporary debugging container is often called an *ephemeral container*. This is useful when the base container image doesn't include tools like `curl`, `bash`, or `nslookup`.

**Command:**
```shell
kubectl debug -it debug-demo -n demo
```

**Response:**
```shell
Defaulting debug container name to debugger-74jc9.
All commands and output from this session will be recorded in container logs, including credentials and sensitive information passed through the command prompt.
If you dont see a command prompt, try pressing enter.
# ping db-postgres
PING db-postgres (10.244.1.20): 56 data bytes
64 bytes from 10.244.1.20: seq=0 ttl=64 time=0.234 ms
```

From here, you can continue your investigation. This command allows you to troubleshoot a running pod with more options and no risk of impacting the production pod.  

For more information, see [kubectl debug](https://kubernetes.io/docs/reference/kubectl/generated/kubectl_debug/) and [Debug Running Pods with Ephemeral Containers](https://kubernetes.io/docs/tasks/debug/debug-application/debug-running-pod/).

## References
- [Command line tool (kubectl)](https://kubernetes.io/docs/reference/kubectl/)
- [Kubectl Commands - Getting Started](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-)
- [Kubectl in the Spectro Cloud docs](https://docs.spectrocloud.com/clusters/cluster-management/palette-webctl/)
- [What is Kubernetes](https://kubernetes.io/docs/concepts/overview/)
