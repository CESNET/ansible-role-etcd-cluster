# perun-etcd-cluster
Ansible role cesnet.etcd-cluster installs etcd cluster in multi-node configuration, secured with TLS.

## Requirements
* Debian 12 or above.
* etcd-cluster group defined

### TLS certificates must be present for each node in {{ inventory_hostname }}/etcd:
* client-ca-cert.pem - Certificate authority of clients, allowed to connect to etcd.
* peer-ca-cert.pem - Certificate authority of etcd peers, allowed to communicate within a cluster.
* peer-cert.pem - Certificate to authenticate with etcd cluster peers.
* peer-key.pem
* server-cert.pem - Certificate to present to incoming clients, connecting to etcd.
* server-key.pem

**The certificate must have the inventory_hostname (and/or IP address) in the SAN.**

## Role variables
* etcd_cluster - sets ETCD_INITIAL_CLUSTER variable in configuration (defaults to all nodes).
* etcd_initial_cluster_token - sets ETCD_INITIAL_CLUSTER_TOKEN.
* etcd_cluster_group_name - name of the Ansible inventory host group, where etcd will be deployed.

## Example playbook
```
- hosts: etcd-cluster
  become: true
  vars:
    - etcd_initial_cluster_token: Pa$$w0rd123
  roles:
    - cesnet.etcd-cluster
```