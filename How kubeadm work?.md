# How kubeadm work, what is the work flow looks like?

## kube init

This command initializes a Kubernetes control plane node.

### Init workflow

1. Runs a series of pre-flight checks to validate the system state before making changes.
2. Generates a self-signed CA to set up identities for each component in the cluster.
3. Writes kubeconfig files in /etc/kubernetes/ for the kubelet, the controller-manager, and the scheduler to connect to the API server, each with its own identity. Also additional kubeconfig files are written, for kubeadm as administrative entity (admin.conf) and for a super admin user that can bypass RBAC (super-admin.conf).
4. Generates static Pod manifests for the API server, controller-manager and scheduler. Static Pod manifests are written to /etc/kubernetes/manifests; the kubelet watches this directory for Pods to create on startup.
5. Apply labels and taints to the control plane node so that no additional workloads will run there.
6. Generates the token that additional nodes can use to register themselves with a control plane in the future.
7. Makes all the necessary configurations for allowing node joining with the Bootstrap Tokens and TLS Bootstrap mechanism.
8. Installs a DNS server (CoreDNS) and the kube-proxy addon components via the API server.

## kubeadm join

This command initializes a new Kubernetes node and joins it to the existing cluster.

When joining a kubeadm initialized cluster, we need to establish bidirectional trust.
This is split into discovery (having the Node trust the Kubernetes Control Plane) and TLS bootstrap (having the Kubernetes Control Plane trust the Node).

There are two main schemes for discovery.

- The first is to use a shared token along with the IP address of the API server.
- The second is to provide a file - a subset of the standard kubeconfig file. The discovery/kubeconfig file supports token, client-go authentication plugins ("exec"), "tokenFile", and "authProvider".

The TLS bootstrap mechanism is also driven via a shared token.

- This is used to temporarily authenticate with the Kubernetes Control Plane to submit a certificate signing request (CSR) for a locally created key pair.

### The join workflow

1. kubeadm downloads necessary cluster information from the API server. By default, it uses the bootstrap token and the CA key hash to verify the authenticity of that data. The root CA can also be discovered directly via a file or URL.

2. Once the cluster information is known, kubelet can start the TLS bootstrapping process.

The TLS bootstrap uses the shared token to temporarily authenticate with the Kubernetes API server to submit a certificate signing request (CSR); by default the control plane signs this CSR request automatically.

3. Finally, kubeadm configures the local kubelet to connect to the API server with the definitive identity assigned to the node.
