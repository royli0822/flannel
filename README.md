# Flannel Automated Installation and Configuration

Automate Flannel CNI plugin configuration using Ansible Playbook.

## Features

This playbook will automatically:

1. Download the latest `kube-flannel.yml`
2. Check and modify Network CIDR to `10.244.0.0/16` (if different)
3. Add `--iface=nebula1` to flannel container arguments
4. Apply configuration to Kubernetes
5. Restart flannel pods to apply changes
6. Display configuration results and network interface status

## Usage

### Basic Execution
```bash
ansible-playbook flannel-install.yml
```

### With sudo
```bash
ansible-playbook flannel-install.yml --ask-become-pass
```

### Custom Parameters
```bash
# Use different network interface
ansible-playbook flannel-install.yml -e "custom_interface=eth1"

# Use different CIDR
ansible-playbook flannel-install.yml -e "cluster_cidr=10.100.0.0/16"

# Customize both
ansible-playbook flannel-install.yml -e "custom_interface=eth1 cluster_cidr=10.100.0.0/16"
```

## Default Configuration

- **Network Interface**: `nebula1`
- **Cluster CIDR**: `10.244.0.0/16`
- **Manifest Path**: `/tmp/kube-flannel.yml`

## Verification

After execution, it will automatically display:
- `/run/flannel/subnet.env` configuration content
- `flannel.1` network interface status
- Configuration modification summary

Manual verification commands:
```bash
# Check flannel pods
kubectl get pods -n kube-flannel

# Check subnet configuration
cat /run/flannel/subnet.env

# Check network interface
ip link show | grep flannel.1
```

## Modifications Explained

### 1. Network CIDR Modification
```yaml
net-conf.json: |
  {
    "Network": "10.244.0.0/16",  # Ensures this CIDR is used
    ...
  }
```

### 2. Network Interface Parameter
```yaml
- args:
  - --ip-masq
  - --kube-subnet-mgr
  - --iface=nebula1  # Added this line
```
