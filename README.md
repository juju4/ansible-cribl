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

# If not empty, will be use to restrict service communication with systemd
cribl_wep_proxy: ''

cribl_inputs_template: inputs.yml
cribl_pipelines_templates:
  - name: slicendice
    template: slicendice-conf.yml
# Example. To store in vault. Cribl will hash password on first read of users.json.
# Exclude default admin user
cribl_users:
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

* For cribl users, the creation works but not the initial password which has to be reset from Web UI.

* You can test logs ingestion with local logs aka [File Monitor](https://docs.cribl.io/stream/sources-file-monitor) and logger command
```
logger "local logger test from `hostname -s`"
```
Ensure to provide read access to cribl user, for example with acl:
```
sudo setfacl -m u:_cribl:r /var/log/syslog
```

* Worker activity from logs
```
cat /opt/cribl/log/worker/*/cribl.log | jq -r 'select(.clientip == "w.x.y.z") | [.level, .url, .method, .status, .size]'
```

* Source logs "Premature close" errors
```
message:Dropping request due to error
error:Premature close
method:POST
src: 127.0.0.1:<random>
statusCode:500
url:/_bulk
```

* Elasticsearch source
  * No data ingestion. Check if not a beat-server version mismatch even if minor version, update+commit in cribl and restart cribl.
On source beat log
```
Connection marked as failed because the onConnect callback failed: Elasticsearch is too old. Please upgrade the instance. If you would like to connect to older instances set output.elasticsearch.allow_older_versions to true. ES=8.4.3, Beat=8.6.2
```
  * Server returns HTTP 503 Service Unavailable error for Elasticsearch source
```
{"text":"Server is busy, max active connections reached","code":9}
```

* Test Raw HTTP with curl - NOK
```
curl -v -X POST http://localhost:10081 -d 'Test Raw HTTP from curl POST'
```

* Eventually use a local file destination to help troubleshooting. If using a Filesystem destination, this will be a directory with multiple json output files.

* cribl_api_password should be left as is on first run to match default password. After first admin login and password change, it should be updated to new one.

## Resources

* [Common Errors and Warnings](https://docs.cribl.io/stream/common-errors)
* [Bifurcating Observability Data To Multiple Destinations, Jan 2023](https://cribl.io/blog/bifurcating-observability-data-to-multiple-destinations/)

## License

BSD 2-clause
