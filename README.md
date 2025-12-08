Container Runtime (CRI) Ansible
================================

Author:  Timothy C. Arland <tcarland at gmail dot com>
Version: 25.12.08

An Ansible playbook for installing container engines such as *Containerd*
or *cri-o*.

By default, the playbook installs *containerd* with *runc*, with additional
roles for installing support for *buildkit* and *rootless* containers.

There are other container engine playbooks related to Kubernetes, such as
*Kubespray*, but this playbook is intended more for development or desktop
environments that prefer to either replace Docker or closer emulate
production non-docker nodes using containerd or cri-o.

**Note** The playbook will support *cri-o* at some point, but currently
only supports *containerd*.


## Components Matrix

- ***runc*** [v1.3.4](https://github.com/opencontainers/runc)
- ***containerd*** [v2.2.0](https://github.com/containerd/containerd)
- ***cni-plugins*** [v1.8.0](https://github.com/containernetworking/plugins)
- ***cri-tools*** [v1.34.0](https://github.com/kubernetes-sigs/cri-tools)
- ***nerdctl*** *optional* [v2.2.0](https://github.com/containerd/nerdctl)
- ***buildkit*** *optional* [v0.26.2](https://github.com/moby/buildkit)
- ***rootlesskit*** *optional* [v2.3.5](https://github.com/rootless-containers/rootlesskit)
- ***slirp4netns*** *optional* [v1.3.3](https://github.com/rootless-containers/slirp4netns)

<br>

---

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
      enable_cpu_delegation: true
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
    enable_cpu_delegation: false
```

### Configure a Private Registry

Setting the vars `registry_host` and `registry_cacert` will trigger the
playbook to generate the necessary *hosts.toml* file for *containerd*.
The `registry_cacert` should be the name of the *PEM* file as installed
to */etc/ssl/certs*. The playbook does not install the cert nor verify
its existence.

To install a CA Certificate on Debian/Ubuntu distributions:
```sh
sudo cp mycacert.crt /usr/local/share/ca-certificates
sudo update-ca-certificates
ls /etc/ssl/certs/mycacert.pem
```

## Run the Ansible Playbook

### Using Rootless mode

For Ubuntu/Debian systems or any system with AppArmor, add the following
sysctl setting to allow for the creation of the unprivileged namespace.
```
kernel.apparmor_restrict_unprivileged_userns = 0
```

The playbook will also touch '/etc/apparmor.d/' files accordingly.

Technically the inventory file of *./hosts.yml* is considered the default inventory file
and could be excluded, but a typical playbook run would be as follows:
```sh
ansible-playbook -i hosts.yml cri-install.yml
```

## List of configurable variables

|      variable name        |         Description               |    Default     |
| ------------------------- | --------------------------------- | -------------- |
| **container_engine**      | Chooses the container_engine to install, containerd or cri-o | containerd |
|   **cni_subnet**          | The subnet prefix for the container network | 175.17.1.0/24 |
|    **cni_name**           | The name of the container network | crinet  |
|  **cni_interface**        | The name of the network interface |  cni0   |
|  **cni_plugin_type**      | The CNI plugin type to use.       | bridge  |
|  **enable_nerdctl**       | Installs contaiNERDctl CLI        |  true   |
| **enable_buildkit**       | Installs the buildkit component for building containers  |  true   |
| **enable_rootlesskit**    | Installs the kit for enabling rootless containers  |  true  |
| **enable_cpu_delegation** | Support for setting CPU, CPUSET and I/O Delegation to container limits | false |
| **create_docker_link**    | Creates a symlink from docker to nerdctl  | false  |
| **set_user_cni_config**   | Syncs the system CNI config to the userspace config (when rootless is enabled) | true |
|   **registry_host**       | Configures a private registry host  |  ''   |
|  **registry_cacert**      | Configures a private CA cert for the registry host | ''  |
