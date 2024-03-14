# Tokens

In Kubernetes, tokens are generated by different components of the system:

1. **Bootstrap Tokens**:
   * Bootstrap tokens are typically generated during the initialization of a Kubernetes cluster. The process of generating bootstrap tokens is usually handled by the cluster administrator or the tool used for cluster deployment (such as kubeadm).
   * During cluster bootstrap, administrators can specify parameters such as token TTL (time-to-live) and token ID prefix to generate bootstrap tokens. These tokens are then distributed to the nodes or components that need to join the cluster during the bootstrap process.
2. **ServiceAccount Tokens**:
   * ServiceAccount tokens are automatically generated by the Kubernetes API server when a ServiceAccount resource is created within a namespace.
   * When a ServiceAccount is created or updated, the Kubernetes API server generates a secret containing a token for that ServiceAccount.
   * The ServiceAccount token is then mounted into pods by the kubelet when the pod is created, allowing the pod to authenticate with the Kubernetes API server.

In summary, while bootstrap tokens are manually generated by administrators during cluster initialization, ServiceAccount tokens are automatically generated by the Kubernetes API server when ServiceAccount resources are created, and they are managed by the Kubernetes control plane.