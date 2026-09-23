# GitHub Actions DevOps Infra — Terraform

Provisions one AWS EC2 instance and installs Docker, Java/Maven, JFrog Artifactory, SonarQube and HashiCorp Vault on it over SSH.

## Prerequisites

- Terraform installed
- AWS credentials configured 
- `ssh` and `scp` available locally (used by Terraform to provision the instance and fetch `vaultkey.txt`)


## Deploy

```bash
terraform init
terraform plan
terraform apply
```

This takes several minutes: it creates the instance, then installs everything over SSH.

## Setup after `apply`

Run `terraform output` to get the URLs and SSH command for the instance.

### 1. JFrog

1. Open `JFROG_URL` (port `8082`).
2. Log in with `admin` / `password`.
3. When prompted, set the new password to `DevOps1234`.
4. Create a local repository. Name: `devops-project`, Type `Generic`

### 2. Vault

1. Read the root token from the file Terraform pulled down locally:
   ```bash
   cat vaultkey.txt
   ```
2. Open `HASHICORP_VAULT_URL` (port `8200`) → **Sign in with Token** → paste the `Initial Root Token`.

### 3. Verify the JFrog secret in Vault

In the Vault UI, browse the `secrets/` KV engine to `creds/jfrog` — it should show `username=admin`, `password=DevOps1234` (or run `vault kv get secrets/creds/jfrog`).

### 4. SonarQube (Optional: Skip this if you are using SonarCloud )

Open `sonarqube_url` (port `9000`), log in with `admin` / `admin`, and set a new password.


## Destroy

```bash
terraform destroy
```

Local files (`server_key.pem`, `vaultkey.txt`) are not removed automatically.
