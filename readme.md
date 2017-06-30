![coreos etcd cluster](https://github.frapsoft.com/top/open-source-v1.png)

# CoreOS etcd Cluster

_Quick introduction how to setup a etcd Cluster with CoreOS_

## Configuration:

`cloud-config.yml`:

```
#cloud-config

coreos:
  
  etcd2:

    # get a token https://discovery.etcd.io/new?size=3 
    # size = how many etcd cluster nodes
    discovery: "https://discovery.etcd.io/ADD-YOUR-TOKEN-HERE"
    advertise-client-urls: "http://$public_ipv4:2379"
    initial-advertise-peer-urls: "http://$private_ipv4:2380"
    listen-client-urls: "http://0.0.0.0:2379,http://0.0.0.0:4001"
    listen-peer-urls: "http://$private_ipv4:2380,http://$private_ipv4:7001"

  update:
    reboot-strategy: "etcd-lock"

  units:
    - name: "etcd2.service"
      command: "start"
      enable:  true

write-files:

  - path: "/home/core/.bash_profile"
    permissions: "0644"
    content: |
      alias l="ls -alF"
      alias ..="cd .."
      
  - path: "/root/.bash_profile"
    permissions: "0644"
    content: |
      alias l="ls -alF"
      alias ..="cd .."
```

## Installation

```
# validate config
coreos-cloudinit -validate --from-file cloud-config.yml

# configuration (on the fly)
coreos-cloudinit --from-file cloud-config.yml

# install with cloud-init
coreos-install -c cloud-config.yml -C stable -d /dev/sda

# install with ignition
coreos-install -i cloud-ignition.json -C stable -d /dev/sda
```

## etcd usage

**set value:**

`etcdctl set /foo/bar "hello world"`

**get value:**

`etcdctl get /foo/bar`

**watch value:**
 
`etcdctl watch --recursive /foo`

**exec-watch:**

`etcdctl exec-watch --recursive /foo -- sh -c 'echo "\"$ETCD_WATCH_KEY\" key was updated to \"$ETCD_WATCH_VALUE\" value by \"$ETCD_WATCH_ACTION\" action"'`

**swap values:**

**Info:** change value `/foo/bare` to `hello mars` if the current value is `hello world`

`etcdctl set /foo/bar "core" --swap-with-value "hello"`

**TTL:**

`etcdctl set /foo/bar "time to life is 5s" --ttl 5`

## Scripting

`ETCD_ENDPOINT="$(ifconfig docker0 | awk '/\<inet\>/ { print $2}'):2379"`

## Debugging

- `cat /var/lib/coreos-install/user_data`
- `cat /run/systemd/system/etcd2.service.d/20-cloudinit.conf`
- `systemctl status etcd2.service`

## Contact

[![Github](https://github.frapsoft.com/social/github.png)](https://github.com/ellerbrock/)[![Docker](https://github.frapsoft.com/social/docker.png)](https://hub.docker.com/u/ellerbrock/)[![npm](https://github.frapsoft.com/social/npm.png)](https://www.npmjs.com/~ellerbrock)[![Twitter](https://github.frapsoft.com/social/twitter.png)](https://twitter.com/frapsoft/)[![Facebook](https://github.frapsoft.com/social/facebook.png)](https://www.facebook.com/frapsoft/)[![Google+](https://github.frapsoft.com/social/google-plus.png)](https://plus.google.com/116540931335841862774)[![Gitter](https://github.frapsoft.com/social/gitter.png)](https://gitter.im/frapsoft/frapsoft/)

## License 

<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a> [![MIT license](https://badges.frapsoft.com/os/mit/mit-125x28.png?v=103)](https://opensource.org/licenses/mit-license.php)

This work by <a xmlns:cc="http://creativecommons.org/ns#" href="https://github.com/ellerbrock" property="cc:attributionName" rel="cc:attributionURL">Maik Ellerbrock</a> is licensed under a <a rel="license" href="https://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a> and the underlying source code is licensed under the <a rel="license" href="https://opensource.org/licenses/mit-license.php">MIT license</a>.
