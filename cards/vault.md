# DevOps::Vault

## 4 Vault Components

- Storage Backends
- Secrets Engines (core functionality)
- Authentication Methods
- Audit Devices


## When would I seal Vault?

- Key shards are inadvertently exposed.
- Detection of a compromise or network intrusion.
- Spyware/malware on the Vault nodes.


## 3 Ways to Unseal Vault

- Key Sharding (Shamir)
- Cloud Auto Unseal (requires a cloud provider)
- Transit Auto Unseal (requires another Vault cluster)


## Starting the Vault dev server

```bash
vault server -dev
export VAULT_ADDR=http://127.0.0.1:8200
export VAULT_TOKEN=AddYourVaultTokenHere
vault login
```

## Create or update a secret

```bash
vault kv put \
  secret/${keyname} key=value
```

via API (and sending a file)
```bash
curl \
  --header "X-Vault-Token: ${VAULT_TOKEN}" \
  --request POST \
  --data @${file} \
  "VAULT_ADDR/v1/secret/data/${keyname}"
```

## Get a secret

```bash
vault kv get \
  secret/${keyname}
```

via API
```bash
curl \
  --header "X-Vault-Token: ${VAULT_TOKEN}" \
  "VAULT_ADDR/v1/secret/data/${keyname}"
```

## Get a specific version of secret

```bash
vault kv get -version=1 \
  secret/${keyname}
vault kv get -version=2 \
  secret/${keyname}
```

via API
```bash
curl \
  --header "X-Vault-Token: $VAULT_TOKEN" \
  "VAULT_ADDR/v1/secret/data/${keyname}?version=1"
```


## Delete a secret

```bash
vault kv delete secret/${keyname}
```

via API
```bash
curl \
  --header "X-Vault-Token: ${VAULT_TOKEN}" \
  --request DELETE \
  "VAULT_ADDR/v1/secret/data/${keyname}"
```


## Undelete a deleted secret

need to specify the versions:
```bash
vault kv undelete -versions=2 \
  secret/${keyname}
```

via API (note the path)
```bash
curl \
  --header "X-Vault-Token: $VAULT_TOKEN" \
  --request POST \
  --data '{"versions": [2]}' \
  "VAULT_ADDR/v1/secret/undelete/${keyname}"
```



## Destroy the secrets

need to specify the versions
```bash
vault kv destroy \
  -versions=1,2 \
  secret/${keyname}
```

via API (note the path)
```bash
curl \
  --header "X-Vault-Token: $VAULT_TOKEN" \
  --request POST \
  --data '{"versions": [1,2]}' \
  "VAULT_ADDR/v1/secret/destroy/${keyname}"
```


## Remove all data about the secrets

```bash
vault kv metadata delete \
  secret/${keyname}
```

via API
```bash
curl \
  --header "X-Vault-Token: $VAULT_TOKEN" \
  --request DELETE \
  "VAULT_ADDR/v1/secret/metadata/${keyname}"
```


