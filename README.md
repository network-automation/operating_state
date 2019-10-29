# Operating state (WIP)

`operating_state` is an Ansible role that collects operating state information about various resources on network devices and returns parsed and structured data.

#### The following resources are current supported:
- arp
- cdp
- fex
- hardware
- interfaces
- lldp
- mac
- spanning_tree
- system
- version
- vlan
- vlan.private_vlan
- vpc

#### The following network operating systems are currently supported:
- ios/iosxe
- nxos
- eos

#### The following parser engines are supported:
- native_json: Use the network device's native json support
- native_xml: Use the network device's native xml support
- pyats: Use the Cisco pyats/genie library


## Quick start

```yaml
- hosts: all
  gather_facts: false
  roles:
  - role: operating_state
    gather_states:
    - all
  tasks:
  - debug:
      var: ansible_facts
```

# Role parameters

- `gather_states`: (required) Gather the operating state for a list of resources.  `all` will gather all available resources. Combined with `all`, `!resource` will exclude the resource.
- `gather_states_engine` (optional) Set the parsing engine for a particular network operating system.
- `fact_key` (optional) Store the gathered state information in a new root fact key.

## Use cases:

#### Only gather a specfic resource

```yaml
- hosts: all
  gather_facts: false
  roles:
  - role: operating_state
    gather_states:
    - interfaces
  tasks:
  - debug:
      var: ansible_facts
```

#### Gather all, excluding a specfic resource

```yaml
- hosts: all
  gather_facts: false
  roles:
  - role: operating_state
    gather_states:
    - all
    - !interfaces
  tasks:
  - debug:
      var: ansible_facts
```

#### Gather all resource statistics before and after a network change and compare

```yaml
- hosts: all
  gather_facts: false
  roles:
  - role: operating_state # this is necessary to register the plugins
  tasks:
  - include_role:
      name: operating_state
    vars:
      fact_key: before
      only_stats: True
      gather_states:
      - all
  - debug:
      msg: Make changes here
  - include_role:
      name: operating_state
    vars:
      fact_key: after
      only_stats: True
      gather_states:
      - all

  - name: Compare the before and after in dotted format
    fact_diff:
      before: "{{ before|default({})|to_dotted }}"
      after: "{{ after|default({})|to_dotted }}"
```

## Additional documentation

Additional documentation for the included filter and action plugins can be found in the docs folder.
