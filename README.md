etc_hosts
=========

This role is used in a rootless and possibly dns void world to ensure any endpoints your deployment depends on resolve on the host.  The resolution can be either /etc/hosts or via DNS.  If the FQDN is unable to resolve, the task will fail.  If the FQDN resolves, then the IPv4 will be checked to ensure it matches.  If the match fails, the task will fail.

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

ansible-playbook -i \<inventory file\> \<playbook name\> -e "targets=\<deploy targets\>"

```
- hosts: "{{ targets }}"

  vars:
    etc_hosts_deps:
      - ws-sips
      - ws-coffee

  roles:
    - etc_hosts
```

Sample Task Outputs
------------------

Task where entries are found and match the expected IP

```
TASK [etc_hosts : check for any etc hosts dependencies] *********************************************************
ok: [localhost] => (item=ws-sips)
ok: [localhost] => (item=ws-coffee)

TASK [etc_hosts : validate ipv4 matches expected value] *********************************************************
skipping: [localhost] => (item=None) 
skipping: [localhost] => (item=None) 
```

Task where an entry does not resolve at all
```
TASK [etc_hosts : check for any etc hosts dependencies] *********************************************************
failed: [localhost] (item=ws-sips) => {
    "changed": false, 
    "item": "ws-sips"
}

MSG:

One or more supplied key could not be found in the database.
```

Task where entry resolves, but to a non-matching IP
```
TASK [etc_hosts : check for any etc hosts dependencies] *********************************************************
ok: [localhost] => (item=ws-sips)
ok: [localhost] => (item=ws-coffee)

TASK [etc_hosts : validate ipv4 matches expected value] *********************************************************
failed: [localhost] (item={'_ansible_parsed': True, 'changed': False, '_ansible_no_log': False, 'item': u'ws-sips', '_ansible_item_result': True, 'failed': False, u'invocation': {u'module_args': {u'fail_key': True, u'split': None, u'key': u'ws-sips.cluster.local', u'database': u'hosts'}}, u'ansible_facts': {u'getent_hosts': {u'192.68.241.208': [u'ws-sips.cluster.local']}}, '_ansible_ignore_errors': None}) => {
    "changed": false, 
    "item": {
        "ansible_facts": {
            "getent_hosts": {
                "192.68.241.208": [
                    "ws-sips.cluster.local"
                ]
            }
        }, 
        "changed": false, 
        "failed": false, 
        "invocation": {
            "module_args": {
                "database": "hosts", 
                "fail_key": true, 
                "key": "ws-sips.cluster.local", 
                "split": null
            }
        }, 
        "item": "ws-sips"
    }
}

MSG:

Entry IP mismatch ws-sips.cluster.local Expected 192.68.241.207 Received [u'192.68.241.208']
```

License
-------

GNU General Public License v3.0

Author Information
------------------

Bob Boldin
