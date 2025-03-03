Here is the updated code block:

```md 00_instalation/install.md (1-42)
### Installation of Talos Cluster

#### Generate Configuration Files

Generate the configuration files for the cluster using `talosctl`:
```bash
# Create a directory to store the generated configuration files
mkdir ./talos_init

# Generate the controlplane configuration file
talosctl gen config test https://10.0.4.140:6443 --output-dir ./talos_init
```

#### Apply Configuration Files

Apply the configuration files to the nodes:
```bash
# Apply the controlplane configuration
talosctl apply-config --insecure -n 10.0.4.140 --file ./talos_init/controlplane.yaml

# Bootstrap the controlplane node
talosctl bootstrap --nodes 10.0.4.140 --endpoints 10.0.4.140 --talosconfig=./talos_init/talosconfig

# Generate a Kubernetes configuration file for the cluster
talosctl kubeconfig --nodes 10.0.4.140 --endpoints 10.0.4.140 --talosconfig=./talos_init/talosconfig
```

#### Add Worker Nodes

Add additional worker nodes to the cluster:
```bash
# Apply the worker configuration to node 10.0.4.141
talosctl apply-config --insecure --nodes 10.0.4.141 --file ./talos_init/worker.yaml -e 10.0.4.140

# Apply the worker configuration to node 10.0.4.142
talosctl apply-config --insecure --nodes 10.0.4.142 --file ./talos_init/worker.yaml -e 10.0.4.140

# Label the nodes as workers
kubectl label nodes <node-name> role=worker
```

Note: Replace `<node-name>` with the actual name of the node you want to label.

**Prerequisites:** Make sure you have `kubectl` and `talosctl` installed before running these commands.
```bash
# Install kubectl
curl -sfL https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://api.github.com/repos/kubernetes/kubernetes/releases/latest | grep tag_name | cut -d'"' -f4)/bin/linux/amd64/kubectl into /usr/local/bin/kubectl
chmod +x /usr/local/bin/kubectl

# Install talosctl
curl -sfL https://github.com/talos-systems/talos/releases/download/v1.2.0/talosctl-linux-amd64.tar.gz | tar xvf - && sudo mv talosctl-linux-amd64 /usr/local/bin/
chmod +x /usr/local/bin/talosctl
```
**Note:** Replace the versions of `kubectl` and `talosctl` with the latest ones if available.