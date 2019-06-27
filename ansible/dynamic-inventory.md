# Dynamic Inventory

## Base skeleton

Any script that generates a JSON file can be used as an inventory to ansible. Make sure the script is executable (eg: `chmod +x inventory.sh`). 

The basic template that needs to be generated has the following format:

```json
{
    "_meta": {
        "hostvars": {
            "local": {
                "ansible_connection": "local",
                "ansible_host": "127.0.0.1"
            }
        }
    },
    "all": {
        "hosts": []
    },
    "localhost": {
        "hosts": [
            "local"
        ]
    }
}
```

This specific template allows connecting to your localhost instance only. To add extra hosts, simply expand the `hosts` section. For example:

```json
{
    "_meta": {
        "hostvars": {
            "local": {
                "ansible_connection": "local",
                "ansible_host": "127.0.0.1"
            },
            "host-1.internal.example.com": {
                "ansible_host": "172.16.0.1"
            },
            "host-2.internal.example.com": {
                "ansible_host": "172.16.0.2"
            },
            "host-3.internal.example.com": {
                "ansible_host": "172.16.0.3"
            }
        }
    },
    "all": {
    	"hosts": [
    		"host-1.internal.example.com",
            "host-2.internal.example.com",
            "host-3.internal.example.com"    		
    	]
    },
    "frontend": {
    	"hosts": [
    		"host-1.internal.example.com"
    	]
    },
    "backend": {
        "hosts": [
            "host-2.internal.example.com",
            "host-3.internal.example.com"
        ]
    },
    "localhost": {
        "hosts": [
            "local"
        ]
    }    
}
```

By specifying the ip address inside the `hostvars` section, DNS lookups can be skipped. This is not a requirement, but I have found it useful at times to connect to an instance before its DNS entry has propagated entirely, or to avoid split-DNS setup issues where for example `host-1.internal.example.com` will resolve to some internal IP address, while you need to connect to the instance over its public IP address, or some other IP address that is not resolveable over DNS.


To use the custom inventory script, simply pass the `--inventory` or `-i` option to the `ansible-playbook` or `ansible` commands:

```bash
$ ansible -i inventory.sh -m ping all
host-1.internal.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
host-2.internal.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
host-3.internal.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

The other keys (eg: `all`, `frontend`, `backend`) are useful to target only a specific group of machines. You can limit which nodes to run an ansible playbook on by specifying the `--limit` or `-l` commandline option:

```bash
$ ansible -i inventory.sh -l frontend -m ping all
host-1.internal.example.com | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

The above command will only execute command on all hosts that are defined inside the `fronted` group (ie: `host-1.internal.example.com`). In this case, we could have done without the `-l frontend` and simply have specified the group using `-m ping frontend`, but I just wanted to show the general solution of limiting to a group of nodes



## Real world configuration example


