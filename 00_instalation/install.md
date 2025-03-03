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