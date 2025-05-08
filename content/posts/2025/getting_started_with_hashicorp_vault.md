---
title: "Getting Started with HashiCorp Vault"
date: 2025-05-07T19:55:49-04:00
draft: false
---

In modern applications, securing sensitive data like API keys, passwords, and certificates is critical. [**HashiCorp Vault**](https://developer.hashicorp.com/vault) is a powerful open-source tool designed to manage secrets and protect sensitive data.

---

## What Is HashiCorp Vault?

**Vault** helps you:

- Securely store and access secrets
- Dynamically generate credentials (e.g., DB credentials)
- Encrypt/decrypt data without storing it
- Manage access through fine-grained policies

---

## Key Concepts

- **Secrets Engine**: Backend that stores secrets (e.g., KV, AWS, DB).
- **Authentication Method**: How clients authenticate (e.g., Token, AppRole).
- **Policies**: Define who can do what.
- **Leases**: Automatic expiration of secrets.

---

## Getting Started

### 1. Install Vault

#### macOS

```bash
brew install vault
```

#### Windows

##### Chocolately

```sh
choco install vault
```

##### Manual Installation

1. Download the Vault binary from <https://developer.hashicorp.com/vault/downloads>.
2. Unzip and add the Vault executable to your system's PATH.

### 1.1 Running the vault (Dev Mode)

```sh
vault server -dev
```

Vault starts in dev mode with a root token printed in the console.

---

### 2. Export the Token

```bash
export VAULT_ADDR='http://127.0.0.1:8200'
export VAULT_TOKEN='s.xxxxxx'  # from the dev server output
```

---

### 3. Enable KV Secrets Engine

```bash
vault secrets enable -path=secret kv
```

---

### 4. Store and Read a Secret

```bash
vault kv put secret/myapp username=admin password=secret123

vault kv get secret/myapp
```

---

### 5. Set a Policy

```hcl
# myapp-policy.hcl
path "secret/data/myapp" {
  capabilities = ["read"]
}
```

```bash
vault policy write myapp-policy myapp-policy.hcl
```

---

### 6. Create a Token with the Policy

```bash
vault token create -policy=myapp-policy
```

Use this token in your app to read secrets securely.

---

## Dynamic Secrets Example (Database)

Vault can generate database credentials on demand:

```bash
vault secrets enable database

vault write database/config/my-postgres \
    plugin_name=postgresql-database-plugin \
    allowed_roles="readonly" \
    connection_url="postgresql://vaultuser:password@localhost:5432/mydb?sslmode=disable"

vault write database/roles/readonly \
    db_name=my-postgres \
    creation_statements="CREATE ROLE \"{{name}}\" WITH LOGIN PASSWORD '{{password}}' VALID UNTIL '{{expiration}}';" \
    default_ttl="1h" \
    max_ttl="24h"
```

Then request credentials:

```bash
vault read database/creds/readonly
```

---

## Wrap-Up

Vault helps developers:

- Avoid hardcoding secrets
- Enforce access controls
- Rotate credentials automatically

Try integrating Vault in your dev/test workflow and scale as needed.
