### Use Docker Deploy a local testnet 

#### Pull Image
I already build a image, your can pull and local deploy

```
docker pull magichen/libra_e2e:v0.1.0
```

#### Run four docker container as validator
```
docker run -idt --name node_one magichen/libra_e2e:v0.1.0 bash 
docker run -idt --name node_two magichen/libra_e2e:v0.1.0 bash 
docker run -idt --name node_three magichen/libra_e2e:v0.1.0 bash 
docker run -idt --name node_four magichen/libra_e2e:v0.1.0 bash 
```

#### update config file

Use node_one as the seed node
```
# At node_one view the ip address
cd /opt/libra/etc

# Some file at everynode.
vim seed_peers.config.toml  

`
[seed_peers]
8deeeaed65f0cd7484a9e4e5ac51fbac548f2f71299a05e000156031ca78fb9f = ["/ip4/node_one_ip/tcp/30303"]
`

# Different, everynode has different ip address and different peer id.
vim node.config.toml
`
SELE_IP 修改为 node_one ip address
`

vim peer_keypairs.config.toml

# node_one
`
修改peer_keypairs.config.toml 为config 中8deeeaed65f0cd7484a9e4e5ac51fbac548f2f71299a05e000156031ca78fb9f.node.keys.toml
`

# node_two
修改peer_keypairs.config.toml 为config 1e5d5a74b0fd09f601ac0fca2fe7d213704e02e51943d18cf25a546b8416e9e1.node.keys.toml

# node_three
修改peer_keypairs.config.toml 为config ab0d6a54ce9d7fc79c061f95883a308f9bdfc987262b6a34a360fdd788fcd9cd.node.keys.toml

# node_four
修改peer_keypairs.config.toml 为config 57ff83747054695f2228042c26eb6a243ac73de1b9038aea103999480b076d45.node.keys.toml

```

#### Start 
```
# node_one 
/opt/libra/bin/libra_node -f node.config.toml  --peer_id 8deeeaed65f0cd7484a9e4e5ac51fbac548f2f71299a05e000156031ca78fb9f

# node_two 
/opt/libra/bin/libra_node -f node.config.toml  --peer_id 1e5d5a74b0fd09f601ac0fca2fe7d213704e02e51943d18cf25a546b8416e9e1

# node_three
/opt/libra/bin/libra_node -f node.config.toml  --peer_id ab0d6a54ce9d7fc79c061f95883a308f9bdfc987262b6a34a360fdd788fcd9cd

# node_four
/opt/libra/bin/libra_node -f node.config.toml  --peer_id 57ff83747054695f2228042c26eb6a243ac73de1b9038aea103999480b076d45

```

#####
```
docker exec node_five /opt/libra/bin/libra_node -f /opt/libra/etc/node.config.toml --peer_id 8deeeaed65f0cd7484a9e4e5ac51fbac548f2f71299a05e000156031ca78fb9f

docker exec node_two /opt/libra/bin/libra_node -f /opt/libra/etc/node.config.toml --peer_id 1e5d5a74b0fd09f601ac0fca2fe7d213704e02e51943d18cf25a546b8416e9e1

docker exec node_three /opt/libra/bin/libra_node -f /opt/libra/etc/node.config.toml --peer_id ab0d6a54ce9d7fc79c061f95883a308f9bdfc987262b6a34a360fdd788fcd9cd

docker exec node_four /opt/libra/bin/libra_node -f /opt/libra/etc/node.config.toml --peer_id 57ff83747054695f2228042c26eb6a243ac73de1b9038aea103999480b076d45

```

#### run client

Edit start_cli_testnet.sh
```
#!/bin/bash

print_help()
{
    echo "Build client binary and connect to testnet."
    echo "\`$0 -r|--release\` to use release build or"
    echo "\`$0\` to use debug build."
}

source "$HOME/.cargo/env"

SCRIPT_PATH="$(dirname $0)"

RUN_PARAMS="--host 127.0.0.1 --port 30307 -s ./trusted_peers.config.toml"

case $1 in
    -h | --help)
        print_help;exit 0;;
    -r | --release)
        echo "Building and running client in release mode."
        cargo run -p client --release -- $RUN_PARAMS
        ;;
    '')
        echo "Building and running client in debug mode."
        cargo run -p client -- $RUN_PARAMS
        ;;
    *) echo "Invalid option"; print_help; exit 0;
esac
```
The run command `sh start_cli_testnet.sh`


