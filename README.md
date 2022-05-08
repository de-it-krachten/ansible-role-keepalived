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
- Ubuntu 22.04 LTS

Note:
<sup>1</sup> : no automated testing is performed on these platforms

Role Variables
--------------
<pre><code>
# VRRP broadcast network address
keepalived_broadcast: '224.0.0.0/8'

# list of VRRP instances (virtual/floating IP)
# each item consists of : name, virtual_router_idm cluster_ip, authentication, pass(phrase)
keepalived_vrrp_instances: []

# list of VRRP sync groups
keepalived_vrrp_sync_groups: []

# keepalive role based on ansible role
keepalived_role: "{{ 'master' if (inventory_hostname in groups['keepalived_master']) else 'backup' }}"

# global options
keepalived_global_options:
  vrrp_skip_check_adv_addr:
  vrrp_garp_interval: 0
  vrrp_gna_interval: 0
  enable_script_security:
  script_user: keepalived

keepalived_vrrp_scripts:
  - name: chk_haproxy
    options:
      script: '"/bin/pidof haproxy"'
      interval: 5
      init_fail:
</pre></code>


Example Playbook
----------------

<pre><code>
- name: sample playbook for role 'keepalived'
  hosts: all
  vars:
    keepalived_vrrp_sync_groups:
      - name: SG1
        members:
          - vrrp1
          - vrrp2
    keepalived_vrrp_instances:
      - name: vrrp1
        options:
          interface: eth0
          virtual_router_id: 1
          state: "{{ 'MASTER' if keepalived_role == 'master' else 'BACKUP' }}"
          priority: "{{ 150 if keepalived_role == 'master' else 100 }}"
          advert_int: 1
          version: 2
        cluster_ip: 172.17.0.100
        authentication: true
        auth_type: PASS
        auth_pass: TEST1
      - name: vrrp2
        options:
          interface: eth0
          virtual_router_id: 2
          state: "{{ 'MASTER' if keepalived_role == 'master' else 'BACKUP' }}"
          priority: "{{ 150 if keepalived_role == 'master' else 100 }}"
          advert_int: 1
          version: 2
        cluster_ip: 172.17.0.200
        authentication: true
        auth_type: AH
        auth_pass: TEST2
  tasks:
    - name: Include role 'keepalived'
      include_role:
        name: keepalived
</pre></code>
