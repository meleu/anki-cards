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

## Secrets Engines Lifecycle

- Enable
- Disable
- Move
- Tune


## Enable a Secret Engine
```bash
vault secrets enable \
  [options] TYPE
```
Example (key-value type):
```bash
vault secrets enable \
  -path=dev-a kv
```

## List available Secret Engines
```bash
vault secrets list
vault secrets list -format=json
```


## Disable a Secret Engine
```bash
vault secrets disable \
  [options] PATH
```
Example:
```bash
vault secrets disable \
  dev-a
```

## Move a Secret Engine
```bash
vault secrets move \
  [options] SOURCE DESTINATION
```
Example:
```bash
vault secrets move \
  dev-a dev-a-moved
```

## Tune a Secret Engine
```bash
vault secrets tune \
  [options] PATH
```

## List current auth methods

```bash
vault auth list
```

## Enable userpass auth method
```bash
vault auth enable userpass
```


## Get help about the userpass auth method
```bash
vault path-help auth/userpass
```


## Add a user to the userpass auth method
```bash
vault write \
  auth/userpass/users/arthur \
  password=dent
```


## list current users
```bash
vault list auth/userpass/users
```


## start an userpass authenticated session
```bash
vault login \
  -method=userpass \
  username=arthur
```
... get the token and set it to `VAULT_TOKEN`.

## Check current token
```bash
vault token lookup
```


## Reset password (note: only root can do it)
```bash
vault write \
  auth/userpass/users/arthur/password \
  password=tricia
```


## Remove an account
```bash
vault delete auth/userspass/users/arthur
```


## Disable userpass authentication method
```bash
vault auth disable auth/userpass
```