[![CI](https://github.com/de-it-krachten/ansible-role-keepalived/workflows/CI/badge.svg?event=push)](https://github.com/de-it-krachten/ansible-role-keepalived/actions?query=workflow%3ACI)


# ansible-role-keepalived

Role for managing keepalived 



## Dependencies

#### Roles
None

#### Collections
- community.general
- ansible.posix

## Platforms

Supported platforms

- Red Hat Enterprise Linux 8<sup>1</sup>
- Red Hat Enterprise Linux 9<sup>1</sup>
- RockyLinux 8
- RockyLinux 9
- OracleLinux 8
- OracleLinux 9
- AlmaLinux 8
- AlmaLinux 9
- SUSE Linux Enterprise 15<sup>1</sup>
- Debian 11 (Bullseye)
- Debian 12 (Bookworm)
- Ubuntu 20.04 LTS
- Ubuntu 22.04 LTS

Note:
<sup>1</sup> : no automated testing is performed on these platforms

## Role Variables
### defaults/main.yml
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

# User for running keepalived script
keepalived_script_user: keepalived_script

# global options
keepalived_global_options:
  vrrp_skip_check_adv_addr:
  vrrp_garp_interval: 0
  vrrp_gna_interval: 0
  enable_script_security:
  script_user: "{{ keepalived_script_user }}"

keepalived_vrrp_scripts:
  - name: chk_haproxy
    options:
      script: '"/bin/pidof haproxy"'
      interval: 5
      init_fail:
</pre></code>

### defaults/family-Debian.yml
<pre><code>
keepalived_packages:
  - keepalived
  - procps
</pre></code>

### defaults/family-RedHat.yml
<pre><code>
keepalived_packages:
  - ipvsadm
  - keepalived
  - procps
</pre></code>




## Example Playbook
### molecule/default/converge.yml
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
