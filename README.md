# Local opswork with docker

Define a file call `ow.yml` with those parameters

- *SSH_USER*: username for SSH
- *SSH_HOST*: ip of host
- *SSH_KEY*: private key to SSH to instance

`ow` uses above params to SSH into instances and get the JSON.

We will need to bring stack up first. The json can be grab from any
server.

You should add this into `.gitignore` to avoid commiting this.

```
echo ow.yml >> .gitignore
```

## Install

Download `ow` script and put it somewhere in your PATH. I usually put it
in `~/bin/ow` and add `~/bin` to my PATH. It doesn't need root
permission to write to system wide and keep everything clean in my home
folder.

## Run

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
ow test ops-app # un default recipe
ow test ops-app::recipe_name # run only this recipe
ow test ops-app ops-redis # tes multiple recipe
```

When `ow` runs, it automatically SSH into server to get the newest JSON. It
parses JSON data, and create as much as container as possible for all the
instancses. Depend on which instance we want to run, `ow` alter JSON to
change current instances.

It then install cookbook with berks, and run cookbook with the JSON
using this command:

```
sudo chef-client --local-mode  -o 'recipe[recipe_list]' -j opswork.json -l info
```

This's all it does. It isn't perfect but it works and fast enough.
Considering the time it takes to boot a whole cluster with Vagrant
