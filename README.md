# sap-hana-ha-pacemaker ![Ansible Lint](https://github.com/redhat-sap/sap-hana-ha-pacemaker/workflows/Ansible%20Lint/badge.svg?branch=master) ![Ansible Galaxy Import](https://github.com/redhat-sap/sap-hana-ha-pacemaker/workflows/Ansible%20Galaxy%20Import/badge.svg?branch=master)

This role configures pacemaker for an existing SAP HANA System Replication deployment on a 8.x systems.

## Requirements

This role is intended to use on RHEL systems where SAP HANA has been deployed and HANA System Replication is configured between the 2 RHEL Nodes. The following Ansible Roles can be used to get to this desired state:

- [redhat_sap.sap_hana_deployment](https://galaxy.ansible.com/redhat_sap/sap_hana_deployment)
- [redhat_sap.sap_hana_hsr](https://galaxy.ansible.com/redhat_sap/sap_hana_hsr)

It needs access to the software repositories required to deploy RHEL High Availability (see also: [Automated SAP HANA System Replication in Scale-Up in pacemaker cluster](https://access.redhat.com/articles/3004101))

You can use the [redhat_sap.sap_rhsm](https://galaxy.ansible.com/redhat_sap/sap_rhsm) Galaxy Role to automate this process

### Role variables

| variable | info | required? |
|:--------:|:----:|:---------:|
|sap_hana_ha_pacemaker_hana_sid|SAP HANA System ID|yes|
|sap_hana_ha_pacemaker_hana_instance_number|Instance Number|yes, **it must be declared as a string** e.g. "00"|
|sap_hana_ha_pacemaker_secondary_read|Configure HANA second Node as Active/Read-Enabled|no (default false)|
|sap_hana_ha_pacemaker_vip|Virtual IP used for the HANA clustered service|yes|
|sap_hana_ha_pacemaker_secondary_vip|Virtual IP used for the HANA clustered service when second Node is configured as Active/Read-Enabled|only if sap_hana_ha_pacemaker_secondary_read 'true'|
|sap_hana_ha_pacemaker_configure_firewall|Wheter to configure firewall rules for RHEL HA|no (default false)|
|sap_hana_ha_pacemaker_hacluster_password|Password to be set up for 'hacluster' uset|yes|
|sap_hana_ha_pacemaker_cluster_name|Name to be given to the RHEL HA Cluster|no (default 'hana_cluster')
|sap_hana_ha_pacemaker_node1_fqdn|Fully qualified domain name for FIRST node of the cluster used for heartbeat traffic|yes|
|sap_hana_ha_pacemaker_node2_fqdn|Fully qualified domain name for SECOND node of the cluster used for heartbeat traffic|yes|
|sap_hana_ha_pacemaker_node1_ip|IP if the FIRST node of the cluster used for heartbeat traffic|yes|
|sap_hana_ha_pacemaker_node2_ip|IP if the SECOND node of the cluster used for heartbeat traffic|yes|
|sap_hana_ha_pacemaker_stickiness|Wheter to configure resource-stickiness|no (default false)|
|sap_hana_ha_pacemaker_threshold|Wheter to configure migration-threshold|no (default false)|
|sap_hana_ha_pacemaker_use_e4s|Whether to use 'e4s' repositories or not|no (default true)|

### STONITH fencing
The following variables are common to all fencing devices (except those on hyperscalers)

|sap_hana_ha_pacemaker_fencing_device.name|Name of the fencing device|yes|
|sap_hana_ha_pacemaker_fencing_device.type|Fencing device type|yes|This is a list of the most common fencing devices:
fence_vmware_soap - VMWare
fence_ipmilan - IPMI
fence_ilo4_ssh - HP ILO
fence_rhevm - RHV
fence_azure_arm - Azure|
|sap_hana_ha_pacemaker_fencing_device.ip|IP address of the fencing device|yes|
|sap_hana_ha_pacemaker_fencing_device.user|Username to connect to the fencing device|yes|
|sap_hana_ha_pacemaker_fencing_device.pwd|Password to connect to the fencing device|yes|
|sap_hana_ha_pacemaker.host_list|List of nodes controlled by the fencing device|yes (if sap_hana_ha_pacemaker_host_map_list is not specified)|
|sap_hana_ha_pacemaker.host_map_list|Mapping of the hostnames of the nodes that will be controlled by the fencing device to the names or IPs with which they are registered to the fencing device (normally VMs are registered to the fencing device with the VM name instead of the hostname. If the hosts are registered with their actual hostname use sap_hana_ha_pacemaker_host_list instead)|yes (if sap_hana_ha_pacemaker_host_list is not specified)|


## Example Playbook

```yaml
    - hosts: hana
      roles:
        - { role: redhat_sap.sap_hana_ha_pacemaker }
```

## Example Inventory

```yaml
# cat <inventory_dir>/group_vars/hana.yml
## Variables required for 'sap_hana_ha_pacemaker' role
sap_hana_ha_pacemaker_hana_sid: RH1
sap_hana_ha_pacemaker_hana_instance_number: "00"
sap_hana_ha_pacemaker_vip: 192.168.47.100
sap_hana_ha_pacemaker_hacluster_password: "Mysecretpassword"
sap_hana_ha_pacemaker_node1_fqdn: hana-node1.test.local
sap_hana_ha_pacemaker_node2_fqdn: hana-node2.test.local
sap_hana_ha_pacemaker_node1_ip: 192.168.47.21
sap_hana_ha_pacemaker_node2_ip: 192.168.47.22
sap_hana_ha_pacemaker_fencing_device_ip: 192.168.47.11
sap_hana_ha_pacemaker_fencing_device:
  name: "myfencingdevice"
  type: "fence_ipmilan"
  user: "fencing_user"
  pwd: "Mysecretpassword"
  host_list: "host1.example.com,host2.example.com"
  host_map_list: "host1.example.com:VM001;host2.example.com:192.168.47.50"


## License

Apache License 2.0

## Author Information

Red Hat SAP Community of Practice

