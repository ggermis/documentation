# Dynamic Inventory

## Base skeleton

Any script that generates a JSON file can be used as an inventory to ansible. Make sure the script is executable (eg: `chmod +x inventory.sh`). The basic template that needs to be generated has the following format:

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
    }
}
```

By specifying the ip address inside the `hostvars` section, DNS lookups can be skipped. This is not a requirement, but I have found it usefull at times to connect to an instance before its DNS entry has propagated entirely

The other keys (eg: `all`, `frontend`, `backend`) are useful to target only a specific group of machines. You can limit which nodes to run an ansible playbook on by specifying the `--limit` or `-l` commandline options

```bash
$ ansible-playbook -i inventory.py
```

## Real world 