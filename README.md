Container Runtime (CRI) Ansible
================================

An Ansible playbook for installing container engines such as *Containerd* 
or *cri-o*. 

By default, the playbook installs *containerd* with *runc*, with additional
roles for installing support for *buildkit* and *rootless* containers.

There are other container engine playbooks related to Kubernetes, such as 
*Kubespray*, but this playbook is intended more for development or desktop
environments that prefer to either replace Docker or closer emulate a 
production non-docker nodes.

## Components Matrix

- runc
- containerd
- cni-plugins
- cri-tools
- nerdctl
- buildkit
- rootlesskit

