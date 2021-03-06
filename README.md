# kube-centos-ansible

Install kubernetes 1.5 on a cluster of CentOS VMs, including CNI pod networking (defaults to Flannel, also has ability to deploy Weave and Multus).

## Want some more detail?

This document is... Kind of terse. Want a complete walkthrough? Check out my [blog article detailing how to get it going from scratch](http://dougbtv.com/nfvpe/2017/02/16/kubernetes-1.5-centos/).

## Playbooks

| Playbook              | Inventory                        | Purpose                                                          |
|-----------------------|----------------------------------|------------------------------------------------------------------|
| `virt-host-setup.yml` | `./inventory/virthost.inventory` | Provision a virtual machine host                                 |
| `kube-install.yml`    | `./inventory/vms.inventory`      | Install and configure a k8s cluster                              |
| `kube-teardown.yml`   | `./inventory/vms.inventory`      | Runs `kubeadm reset` on all nodes to tear down k8s               |
| `vm-teardown.yml`     | `./inventory/virthost.inventory` | Destroys VMs on the virtual machine host                         |
| `multus-cni.yml`      | `./inventory/vms.inventory`      | Compiles [multus-cni](https://github.com/Intel-Corp/multus-cni)  |
| `vm-attach-disk.yml`  | `./inventory/virthost.inventory` | Attach spare disks to VMs (for GlusterFS, or otherwise)          |
| `gluster-install.yml` | `inventory/vms.inventory`        | Install a GlusterFS cluster across VMs (requires vm-attach-disk) |


*(Table generated with [markdown tables](http://www.tablesgenerator.com/markdown_tables))*

## Usage

Step 1. Modify `./inventory/virthost.inventory` to setup a virt host (skip to step 2 if you already have an inventory)

```
ansible-playbook -i inventory/virthost.inventory virt-host-setup.yml 
```

Step 2. Modify `./inventory/vms.inventory` Setup kube on all the hosts. If you used step 1, towards the end of the output there there will be hints on which IPs for each VM created.

```
ansible-playbook -i inventory/vms.inventory kube-install.yml
```

Want more VMs? Edit the `./vars/all.yml` and add them to the list (and then later to your inventory in step 2)

## Using CRI-O

You can also enable [cri-o](http://cri-o.io/) to have an OCI compatible runtime. Set the `container_runtime` variable in `./vars/all.yml` or as an extra var when you run the playbook: 

```
$ ansible-playbook -i inventory/vms.inventory kube-install.yml -e 'container_runtime=crio'
```


## About

Initially inspired by:

* [k8s 1.5 on Centos](http://linoxide.com/containers/setup-kubernetes-kubeadm-centos/)
* [kubeadm getting started](https://kubernetes.io/docs/getting-started-guides/kubeadm/)



