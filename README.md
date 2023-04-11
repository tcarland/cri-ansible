Container Runtime (CRI) Ansible
================================

Author:  Timothy C. Arland <tcarland@gmail.com>  
Version: 23.04.10

An Ansible playbook for installing container engines such as *Containerd* 
or *cri-o*. 

By default, the playbook installs *containerd* with *runc*, with additional
roles for installing support for *buildkit* and *rootless* containers.

There are other container engine playbooks related to Kubernetes, such as 
*Kubespray*, but this playbook is intended more for development or desktop
environments that prefer to either replace Docker or closer emulate  
production non-docker nodes.

## Components Matrix

- ***runc*** [v1.1.5](https://github.com/opencontainers/runc)
- ***containerd*** [v1.7.0](https://github.com/containerd/containerd)
- ***cni-plugins*** [v1.2.0](https://github.com/containernetworking/plugins)
- ***cri-tools*** [v1.23.0](https://github.com/kubernetes-sigs/cri-tools)
- ***nerdctl*** *optional* [v1.3.0](https://github.com/containerd/nerdctl)
- ***buildkit*** *optional* [v0.11.5](https://github.com/moby/buildkit)
- ***rootlesskit*** *optional* [v1.1.0](https://github.com/rootless-containers/rootlesskit)

## Configure Inventory

The global variables available control the components being installed, 
save for the *runc*, *cni-plugins*, and *cri-tools* (crictl), which are
all installed by the playbook regardless. The optional *buildkit* and 
*rootlesskit* can be controlled by host or group of hosts. Note that 
use of *rootless* containers will install *nerdctl* regardless of the 
variable setting.

Sample Inventory:
```yaml
all:
  hosts:
    dev01:
      ansible_host: 192.168.123.6
    noc01:
      ansible_host: 172.16.18.200
      enable_nerdctl: true
      enable_buildkit: false
      enable_rootlesskit: false
  vars:
    container_engine: "containerd"
    enable_nerdctl: true
    enable_buildkit: true
    enable_rootlesskit: true
```

## Run the Ansible Playbook
Technically the inventory file of *./hosts.yml* is considered the default inventory file
and could be excluded, but a typical playbook run would be as follows:
```
ansible-playbook -i hosts.yml cri-install.yml
```

