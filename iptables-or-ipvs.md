# IPTABLES or IPVS

In Kubernetes, services act as an abstraction layer that decouples the frontend of an application (exposed as a service) from the backend pods that actually handle the workload. When you create a service in Kubernetes, you specify a service type, which can be `ClusterIP`, `NodePort`, `LoadBalancer`, or `ExternalName`.

When you create a `ClusterIP` service, Kubernetes assigns it a virtual IP address (VIP), which acts as a single point of contact for clients to access the service. The `ClusterIP` type exposes the service on an internal IP address reachable only from within the cluster.

To ensure that traffic sent to this `ClusterIP` gets routed to the correct pods on the backend, Kubernetes relies on either IPTABLES or IPVS rules, depending on the chosen networking solution (e.g., kube-proxy mode).

1. **IPTABLES Mode**: In this mode, which is the default networking mode for Kubernetes, kube-proxy creates and manages iptables rules on each node. These rules include NAT rules and packet filtering rules to correctly route traffic to the appropriate backend pods. When a request arrives at the `ClusterIP`, iptables rules are consulted to determine which backend pod should handle the traffic. If the service is using session affinity (sticky sessions), iptables rules are also used to maintain session persistence.
2. **IPVS Mode**: IPVS (IP Virtual Server) is an alternative to iptables for load balancing in the Linux kernel. In this mode, kube-proxy utilizes IPVS to manage the routing of traffic to backend pods. IPVS provides more efficient and scalable load balancing compared to iptables, especially in large-scale deployments with many services and pods.

Regardless of whether IPTABLES or IPVS is used, kube-proxy running on each node ensures that the appropriate rules are in place to route traffic to the backend pods associated with the `ClusterIP` service. These rules are dynamically updated as pods are added or removed from the cluster, ensuring that traffic is always routed correctly even as the cluster scales or experiences changes in pod status.
