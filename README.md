# Local opswork with docker

Define a fie all ow_env with those parameters

- *SSH_USER*: username for SSH
- *SSH_HOST*: ip of host
- *SSH_KEY*: private key to SSH to instance

`ow` uses above params to SSH into instances and get the JSON.

We will need to bring stack up first. The json can be grab from any
server.

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

When `ow` runs, it automatically SSH into server to get the JSON file.
It parses JSON file, and create as much as container as possible for all
the instancs.

It then install cookbook with berks, and run cookbook with the JSON
using this command:

```
sudo chef-client --local-mode  -o 'recipe[recipe_list]' -j opswork.json -l info
```

This's all it does. It isn't perfect but it works and fast enough
