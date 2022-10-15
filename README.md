[![Actions Status - Master](https://github.com/juju4/ansible-cribl/workflows/AnsibleCI/badge.svg)](https://github.com/juju4/ansible-cribl/actions?query=branch%3Amaster)
[![Actions Status - Devel](https://github.com/juju4/ansible-cribl/workflows/AnsibleCI/badge.svg?branch=devel)](https://github.com/juju4/ansible-cribl/actions?query=branch%3Adevel)

# cribl ansible role

A simple ansible role to setup cribl.
* https://cribl.io/download/
* https://docs.cribl.io/stream/getting-started-guide/

## Requirements & Dependencies

### Ansible
It was tested on the following versions:
 * 2.13

### Operating systems

Tested on Ubuntu 20.04, 22.04

## Example Playbook

Just include this role in your list.
For example

```
- host: myhost
  roles:
    - juju4.cribl
```

you probably want to review variables

## Variables

```
cribl_from: 'url'
cribl_url: 'https://cdn.cribl.io/dl/3.5.3/cribl-3.5.3-08986e05-linux-x64.tgz'
cribl_hash_url: 'https://cdn.cribl.io/dl/3.5.3/cribl-3.5.3-08986e05-linux-x64.tgz.sha256'
# cribl_from: 'file'  # from orchestrator
cribl_filepath: ''

cribl_user: _cribl
cribl_userhome: /var/empty
cribl_home: "/opt/cribl"

cribl_wep_proxy: ''

cribl_inputs_template: inputs.yml
cribl_pipelines_templates:
  - name: slicendice
    template: slicendice-conf.yml
# Example. To store in vault. Cribl will hash password on first read of users.json.
cribl_users:
  - username: admin
    first: admin
    last: admin
    email: admin
    roles: admin
    passwd: admin
  - username: john
    first: john
    last: doe
    email: john@example.com
    roles: user
    passwd: john

install_archives: /var/_install
is_container: false
```

## Continuous integration

```
$ pip install molecule docker
$ molecule test
$ MOLECULE_DISTRO=ubuntu:20.04 molecule test --destroy=never
```


## Troubleshooting & Known issues

TBD

## License

BSD 2-clause
