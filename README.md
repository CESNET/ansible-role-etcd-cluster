# perun-etcd-cluster
Ansible role cesnet.etcd_cluster installs etcd cluster in multi-node configuration, secured with TLS.

## Requirements
* Debian 12 or above.

### TLS certificates and keys required
* client-ca-cert.pem - Certificate authority of clients, allowed to connect to etcd.
* peer-ca-cert.pem - Certificate authority of etcd peers, allowed to communicate within a cluster.
* peer-cert.pem - Certificate to authenticate with etcd cluster peers.
* peer-key.pem
* server-cert.pem - Certificate to present to incoming clients, connecting to etcd.
* server-key.pem

The above files must be either copied from the Ansible control machine, using etcd_upload_tls_certificates variable,
or must be already present in /etc/etcd/tls, otherwise the playbook will fail.

**The certificate must have the inventory_hostname (and/or IP address) in the SAN.**

## Role variables
* etcd_cluster - sets ETCD_INITIAL_CLUSTER variable in configuration (defaults to all nodes).
* etcd_initial_cluster_token - sets ETCD_INITIAL_CLUSTER_TOKEN.
* etcd_cluster_group_name - name of the Ansible inventory host group, where etcd will be deployed.
* etcd_upload_tls_certificates - the list of TLS certificates and the corresponding keys to be uploaded. Specify an
empty list [], in case you have the 7 required certificate and key files at the target servers already in /etc/etcd/tls.

## Example playbook
```
- hosts: etcd_cluster
  become: true
  vars:
    etcd_initial_cluster_token: Pa$$w0rd123
    etcd_cluster_group_name: etcd_cluster
    etcd_upload_tls_certificates: # set to [], if all TLS files are already present on each node.
      - "{{ inventory_hostname }}/etcd/server-cert.pem"
      - "{{ inventory_hostname }}/etcd/server-key.pem"
      - "{{ inventory_hostname }}/etcd/peer-cert.pem"
      - "{{ inventory_hostname }}/etcd/peer-key.pem"
      - "{{ inventory_hostname }}/etcd/client-ca-cert.pem"
      - "{{ inventory_hostname }}/etcd/peer-ca-cert.pem"
  roles:
    - cesnet.etcd_cluster
```