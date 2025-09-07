# Kubectl Debugging Commands

Kubectl is a command line interface (CLI) tool for interacting with Kubernetes clusters (K8s) via the Kubernetes API server. For more information on kubectl, see [Command line tool (kubectl)](https://kubernetes.io/docs/reference/kubectl/). For kubectl in Palette, see [Kubectl in the SpectroCloud docs](https://docs.spectrocloud.com/clusters/cluster-management/palette-webctl/).

The commands executed in kubectl run against Kubernetes clusters. Here are useful kubectl commands for debugging in order of operation:

| Command | Description | Usage |
| ----------- | ----------- | ----------- |
| `get pods` | Lists all available pods and their status. **Note:** Make sure to specify the `namespace`.| `kubectl get pods --NAMESPACE` |
| `logs` | Retrieves logs of a specific pod, used to review logs or debug a container | `kubectl logs [-f] [-p] (POD NAME) [-c CONTAINER]` |
| `exec` | Executes a command in a container, used to debug a container from the inside or to explore the the enviroment of the container itself | `kubectl exec (POD NAME) [-c CONTAINER] [flags] -- COMMAND [args...]` |
| `debug` | Creates a clone of a pod (inactive debugging container) that does not terminate if an error is experienced inside the container | `kubectl debug (POD NAME) [ -- COMMAND [args...] ]` |

## References
- [Command line tool (kubectl)](https://kubernetes.io/docs/reference/kubectl/)
- [Kubectl Commands - Getting Started](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-strong-getting-started-strong-)
- [Kubectl in the SpectroCloud docs](https://docs.spectrocloud.com/clusters/cluster-management/palette-webctl/)
- [What is Kubernetes](https://kubernetes.io/docs/concepts/overview/)
