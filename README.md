[![CI](https://github.com/de-it-krachten/ansible-role-keepalived/workflows/CI/badge.svg?event=push)](https://github.com/de-it-krachten/ansible-role-keepalived/actions?query=workflow%3ACI)


# ansible-role-keepalived

Role for managing keepalived 


Platforms
--------------

Supported platforms

- Red Hat Enterprise Linux 7<sup>1</sup>
- Red Hat Enterprise Linux 8<sup>1</sup>
- CentOS 7
- RockyLinux 8
- AlmaLinux 8<sup>1</sup>
- Debian 10 (Buster)
- Debian 11 (Bullseye)
- Ubuntu 18.04 LTS
- Ubuntu 20.04 LTS

Note:
<sup>1</sup> : no automated testing is performed on these platforms

Role Variables
--------------
<pre><code>
# VRRP broadcast network address
keepalived_broadcast: '224.0.0.0/8'

# list of vrrp
keepalived_configs: []
</pre></code>


Example Playbook
----------------

<pre><code>
- name: sample playbook for role 'keepalived'
  hosts: all
  vars:
    keepalived_role: "{{ 'master' if inventory_hostname is search('node1') else 'backup }}"
    keepalived_configs:
      - name: vrrp1
        virtual_router_id: 1
        cluster_ip: 172.17.0.100
        authentication: false
      - name: vrrp2
        virtual_router_id: 2
        cluster_ip: 172.17.0.200
        authentication: true
        pass: TEST
  tasks:
    - name: Include role 'keepalived'
      include_role:
        name: keepalived
</pre></code>
