# Local opswork with docker

## Install

Download `ow` script and put it somewhere in your PATH. I usually put it
in `~/bin/ow` and add `~/bin` to my PATH. It doesn't need root
permission to write to system wide and keep everything clean in my home
folder.

## Using

### Init

```
ow init
```


This will create file call `ow.yml` with those parameters

- *SSH_USER*: username for SSH
- *SSH_HOST*: ip of host
- *SSH_KEY*: private key to SSH to instance
- *CONTAINER_NAME*: docker image name

`ow` uses above params to SSH into instances and get the JSON.

We will need to bring stack up first. The json can be grab from any
server.

You should add this into `.gitignore` to avoid commiting this.

```
echo ow.yml >> .gitignore
```

### Download JSON file

```
ow getjson
```

This will SSH into host in `ow.yml` and download opsworks json file.

### Run

Given this opsworks repository

```
├── ow_env
├── Berksfile
├── README.md
├── cloudformation
├── ops-app
├── ops-base
├── ops-celery
├── ops-clamav
├── ops-icinga2
├── ops-jenkins
├── ops-nginx
├── ops-nodejs
├── ops-postfix
├── ops-python
├── ops-rabbitmq
├── ops-redis
├── ops-ruby
└── ops-vpn
```

We can test any cookbook with:

```
ow test # run all cookbook
ow test all ops-app # un default recipe
ow test app ops-app::recipe_name # run only this recipe
ow test app1 ops-app ops-redis # tes multiple recipe 
```

instancses. Depend on which instance we want to run, `ow` alter JSON to
change current instances.

It then install cookbook with berks, and run cookbook with the JSON
using this command:

```
sudo chef-client --local-mode  -o 'recipe[recipe_list]' -j opswork.json -l info
```

This's all it does. It isn't perfect but it works and fast enough.
Considering the time it takes to boot a whole cluster with Vagrant

### Port fowarding

On Mac, docker run inside a VM so we cannot easily use container ip to
access a service but have to use docker port mapping. However, since ow
doesn't support this because when we runs a cluster it's complex to
define mapping for each of container. Instead of, we use a simpler
solution, forwarding host to docker instance like this:

```
ow fw master 18080 18080
```

This means that `127.0.0.1:18080` on host will be forward to ip address
of container `master` port 18080
