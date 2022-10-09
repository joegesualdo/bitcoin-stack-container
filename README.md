# Bitcoin Stack Container
> Quickly and easily run a Bitcoin stack to interact with the Bitcoin network

---

**⚠️ This is experimental. Please use at your own risk.⚠️**

---

Current Bitcoin stack layers:
- [Bitcoin Core Full Node](https://bitcoin.org/en/full-node) - Used to interact with the Bitcoin Network
- [Electrs Server](https://github.com/romanz/electrs) - Used to efficiently query certain data

## Run
> Run mainnet
```
$ docker compose -f docker-compose.mainnet.yml --project-name bitcoin-stack-mainnetup
```
> Run testnet
```
$ docker compose -f docker-compose.testnet.yml --project-name bitcoin-stack-testnet up
```

## Usage:
### Requesting blockchain information
#### From the Bitcoin Core Full Node
> Use bitcoin-cli on your local machine to request information from the node running in the docker container. Username and password can be found in the bitcoin data directory
```
$ bitcoin-cli -testnet -rpcuser=<USERNAME> -rpcpassword=<PASSWORD>getblockchaininfo
```
#### From the Electrum Server
> Use netcat on your local machine to request information from the server running in the docker container
```
// get server version
echo '{"jsonrpc": "2.0", "method": "server.version", "params": ["", "1.4"], "id": 0}' | nc 127.0.0.1 60001

// get balance
$ echo '{"jsonrpc": "2.0", "method": "blockchain.scripthash.get_balance", "id": 0, "params": ["704a62fd3b6e8a7c19d9c0ac0f358c2a728d24ef716f01b6f86bf9efaa078aa0"]}' | nc 127.0.0.1 60001

// get history
echo '{"jsonrpc": "2.0", "method": "blockchain.scripthash.get_history", "id": 0, "params": ["0e96c20579a73b026d1f876a2b35ed3ef396b73bf1ad5562679c1086a27847cf"]}' | nc 127.0.0.1 60001

// list unspent
 echo '{"jsonrpc": "2.0", "method": "blockchain.scripthash.listunspent", "id": 0, "params": ["0e96c20579a73b026d1f876a2b35ed3ef396b73bf1ad5562679c1086a27847cf"]}' | nc 127.0.0.1 60001
```

#### Can I use curl instead of netcat for requesting information from the Electrum Server? 
No. This is because curl sends http headers (and method/host) but electrum only wants the body. I wasn't able to find a way to just send the body without any http info. To see this, start a netcat server ($ nc -l 10839) then send a request to that port ("127.0.0.1:10839") using both curl and netcat and see the difference in the request.


### SSH into the container
```
// ssh into the bitcoind mainnet server
docker exec -it docker exec -it bitcoind /bin/bash /bin/bash

// ssh into the electrs mainnet server
docker exec -it docker exec -it electrs/bin/bash /bin/bash

// ssh into the bitcoind testnet server
docker exec -it docker exec -it bitcoind-testnet /bin/bash /bin/bash

// ssh into the electrs testnet server
docker exec -it docker exec -it electrs-testnet /bin/bash /bin/bash
```

## Requirements 
- [Docker](https://docs.docker.com/get-docker/)

# Notes
For some reason, when I change the dockerfile, changes are not reflected when I run docker compose. So to reflect them, I do the following:
```
docker stop $(docker ps -qa) && docker system prune -af --volumes
docker-compose down
docker-compose up --force-recreate
```
## License
MIT © [Joe Gesualdo]()
 

