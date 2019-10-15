# Ansible Playbooks to install an HA Kubernetes (multi-master) cluster using Kubeadm.  

This repository provides Ansible Playbooks to install a Kubernetes HA cluster in an airgapped environment.
- Uses recently GA'd Kubeadm HA joining features


# Prerequisites:
- Install Ansible and a forward proxy on the Ansible host
  - Ansible:
    for macos `brew install ansible`
    for linux `yum install ansible`

- Setup ssh access from Ansible host to Kubernetes nodes.
```ssh-copy-id -i ~/.ssh/id_rsa.pub <user@host>```

# Environment preparation:

Specify the Master and Workers in the `inventory/*cluster*` file:
```
[k8s-masters] # these are all the masters
[k8s-workers] # these are all the worker nodes
```

Update the `inventory/group_vars/*cluster*` section:
- choose the desired versions for kubernetes and docker
- setup the pod network cidr (default setup is for calico - modify in calico.yaml as well)
- specify the version of Helm to use
- specify the Local Storage Provisioner version


# Install a highly available kubernetes using kubeadm

You can now run install-all.yaml playbook to get your cluster setup.
You can also run the different playbooks separately for different purposes (setting up docker, masters, kubeadm, heml ...).

```
ansible-playbook -i inventory/cluster1-prod playbooks/install-all.yaml --private-key=~/.ssh/id_rsa -u %username% -v
```

# Restarting the install:
If you need to restart the process using kubeadm reset, please use the uninstall.yaml playbook that deletes the state from all vms.


# Upgrade a highly available kubernetes using kubeadm

To upgrade the kubernetes control plane run:
```
ansible-playbook -i inventory/cluster1-prod playbooks/upgrade-all.yaml --private-key=~/.ssh/id_rsa -u username -v
```

# What install-all.yaml includes:

- Adding the required yum repositories
- Installing docker
- Installing kubeadm, kubelet and kubectl
- Initializing the first master with etcd and kubernetes-api
- Join replica master nodes to the primary master
- Adding the worker nodes to the cluster
- Installing Helm & Tiller
- Installing Local Storage Provisioner
- Enable Azure AD OIDC authentication

# Restarting the install:

If you need to restart the process using kubeadm reset, please use the uninstall.yaml playbook that deletes the state from all vms.

# To sequentially drain and patch the underlying OS hosts:

```
ansible-playbook -i inventory/cluster1-prod playbooks/os-patch-updates.yaml --private-key=~/.ssh/id_rsa -u username -v
```