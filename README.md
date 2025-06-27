# Running a Replica node

This repo is setup to easily run Tranched replica nodes.

The docker image for Tranched is hosted in a public docker repo: `public.ecr.aws/i6b2w2n6/nitro-node-celestia:v3.3.2`

# Architecture Overview
- l2: this service is the nitro node
- celestia-light-node: this service is the celestia light node that is used to fetch rollup data from the celestia network
- celestia-das-server: this service is the DAS server the nitro node uses to interact with the celestia light node

# Requirements
- 4 core CPU
- 16GB of RAM
- 256GB SSD (the celestia light node needs at least 100GB, nitro node storage requirements scale with the # of txns on the rollup)


# Setup
1. Set the API_KEY_ALCHEMY env var in `.env` with your alchemy API key
2. Run `docker-compose up -d`
3. Wait for the celestia light node to sync to tip, note that this may take up to a day or longer. During this time, you will see these errors in the logs of the `l2` (nitro node) service
```
ERROR[03-01|13:24:44.862] Failed to resolve blob pointer from celestia err="request timed out"
WARN [03-01|13:24:44.862] error reading inbox                      err="request timed out"
```
4. Once the celestia light node completes syncing, you will begin seeing the nitro node building blocks:
```
INFO [03-01|13:26:01.178] created block                            l2Block=1 l2BlockHash=a3a1db..3c1a2d
```
5. It will take a while for the nitro node to sync fully. You can query the node as follows:
```
curl -X POST \
  -H "Content-Type: application/json" \
  --data '{"jsonrpc":"2.0","method":"eth_getBlockByNumber","params":["latest", false],"id":1}' \
  http://localhost:8545
```
