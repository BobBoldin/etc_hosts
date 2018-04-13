Check Etc Hosts
=========

This role is used in a rootless and possibly dns void world to ensure any endpoints your deployment depends on resolve on the host.  The resolution can be either /etc/hosts or via DNS.  If the FQDN is unable to resolve, the task will fail.  If the FQDN resolves, then the IPv4 will be checked to ensure it matches.  If they match fails, the task will fail.

Requirements
------------

This role is designed to be included with other roles.

Role Variables
--------------

Variable structure looks more complex then it needs to be, however this role was designed to work within a deployment framework and is following a general standard.

| Variable Name | Variable TYPE | Location to define or override |    
| --- | --- | --- |    
| etc_hosts | DICTIONARY | group_vars |
| etc_hosts_dep | LIST | playbook |

**group_vars**/*group_name*

    etc_hosts:
      ws-sips:
        fqdn: ws-sips.cluster.local
        ipv4: 192.68.241.207
      ws-coffee:
        fqdn: ws-coffee.cluster.local
        ipv4: 192.68.241.173

**label** Required, and is the key your etc_hosts_deps list from playbook vars will reference

**fqdn** Required, and is the fqdn portion of the endpoint you depend on

**ipv4** Required, and is the ipv4 portion of the endpoint you depend on


Dependencies
------------

None

Example Playbook
----------------

ansible-playbook -i <inventory file> <playbook name> -e "hosts=<deploy targets>"

- hosts: "{{ hosts }}"

  vars:
    etc_hosts_deps:
      - ws-sips
      - ws-coffee

  roles:
    - check_etc_hosts

License
-------

BSD

Author Information
------------------

Bob Boldin
