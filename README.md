# Setting Up a Talos-Based Kubernetes Cluster

This guide provides step-by-step instructions to install and configure Talos and set up a Kubernetes cluster. Talos is a modern, secure operating system for Kubernetes nodes.

## Prerequisites

1. **Machines/VMs for the Cluster**:
   - At least one machine for the control plane.
   - One or more machines for worker nodes.

2. **Tools Installed Locally**:
   - [Talosctl](https://talos.dev/docs/v1.0/introduction/getting-started/#install-talosctl)
   - [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
   - [Helm](https://helm.sh/docs/intro/install/)

3. **Network Access**:
   - Ensure all nodes can communicate over the network.
   - The local machine should be able to reach the control plane node on port `6443`.

---

## Step 1: Generate Talos Configuration Files

Run the following command to generate the Talos configuration for the control plane and worker nodes:

```bash
talosctl gen config talos-datadog-k8s https://192.168.10.13:6443 --output-dir _out
```

- Replace `192.168.10.13` with the desired IP address for the control plane node.
- This will generate two files in the `_out` directory: `controlplane.yaml` and `worker.yaml`.

---

## Step 2: Apply Talos Configuration to Nodes

### Configure the Control Plane Node

Run the following command to apply the configuration to the control plane node:

```bash
talosctl apply-config --insecure --nodes 192.168.10.13 --file _out/controlplane.yaml
```

### Configure Worker Nodes

Run the following command for each worker node:

```bash
talosctl apply-config --insecure --nodes 192.168.10.3 --file _out/worker.yaml
```

- Replace `192.168.10.3` with the IP address of each worker node.

---

## Step 3: Configure Talos Endpoint

Set the Talos configuration endpoint to the control plane node's IP:

```bash
TALOSCONFIG="_out/talosconfig"
export TALOSCONFIG
```

Connect `talosctl` to the control plane:

```bash
talosctl config endpoint 192.168.10.13
```

---

## Step 4: Bootstrap the Kubernetes Control Plane

Run the following command to bootstrap the Kubernetes control plane:

```bash
talosctl bootstrap
```

After bootstrapping, verify the nodes:

```bash
talosctl get nodes
```

---

## Step 5: Install and Configure `kubectl`

1. Download the latest version of `kubectl`:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
```

2. Move `kubectl` to a directory in your `PATH`:

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

3. Verify the installation:

```bash
kubectl version --client
```

4. Copy the `talosconfig` to your Kubernetes configuration directory:

```bash
mkdir -p ~/.kube
cp kubeconfig ~/.kube/config
```

5. Test the connection to the cluster:

```bash
kubectl get nodes
```

---

## Step 6: Install Helm (Optional)

1. Download the Helm installation script:

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
```

2. Run the script to install Helm:

```bash
./get_helm.sh
```

3. Verify the installation:

```bash
helm version
```

---

## Notes

- Replace all IP addresses with the appropriate ones for your setup.
- Use secure methods (e.g., HTTPS and certificates) for production environments.
- After installation, you can deploy workloads and manage your Kubernetes cluster using `kubectl` and `helm`.

---

## Troubleshooting

- If nodes are not visible after bootstrapping, check the network connectivity and ensure the `TALOSCONFIG` file is correctly configured.
- Use the `talosctl logs` command to view logs on the nodes for debugging.


