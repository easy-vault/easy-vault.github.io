---
layout: default
title: Getting Started
nav_order: 2
---

# Setup Vault with Approle Authentication
{: .no_toc }

## start vault container
```bash
docker run -p 1234:1234 --cap-add=IPC_LOCK -e 'VAULT_DEV_ROOT_TOKEN_ID=myroot' -e 'VAULT_DEV_LISTEN_ADDRESS=0.0.0.0:1234' vault
```
## Put secret into vault

1. Create secret engine . eg. kv/test
2. create secret - demo  
3. write data
   
```json
{"say": "Hare Krishna"}
```

## Create Policy
```h
# Allow tokens to look up their own properties
path "kv/test/data/demo" {
    capabilities =  [ "read" ]
}
```
## Create Role “test“ and attach with policy “test“ and create Approle secret.

```bash 
vault write auth/approle/role/test token_policies="test" token_ttl=4h token_max_ttl=4h

vault read auth/approle/role/test/role-id
Key     Value                               
role_id c33d9ce8-0594-e038-ff07-dec972282ca9
  
vault write -force auth/approle/role/test/secret-id
Key                Value                               
secret_id          aef9fb14-fa3d-0b5b-a016-64658475a692
secret_id_accessor da5eb061-d5ef-53c2-14df-fd540fee9bea
secret_id_ttl      0
```

# create an Application which access secrets from vault
Refer [Spring boot example app](https://github.com/easy-vault/easy-vault-example)

# Write Dockerfile 
## import base easy-vault as image 

```dockerfile
FROM ghcr.io/easy-vault/easy-vault:latest AS vault_client
```

## import main base image (JDK for example)

```dockerfile
FROM adoptopenjdk/openjdk11:latest
COPY target/spring-boot-example-0.0.1.jar app.jar
COPY build.sh .
```
