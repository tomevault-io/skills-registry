---
name: whirlwind-server-ansible
description: Configuration management for Whirlwind EC2 hosts using Ansible, SSM connection, dynamic inventory, and CI/CD pipeline. Use when editing playbooks or roles, running Ansible locally or in CodeBuild, wiring SSM or Secrets inputs, or troubleshooting SSM connection failures in this repo. Use when this capability is needed.
metadata:
  author: arnaringig
---

# Whirlwind Server Ansible

## Overview
Configure the EC2 core node and services (Docker, Directus, WireGuard, Nginx, Certbot) with Ansible. This repo is optimized for SSM-based execution from CodeBuild, but can be run locally for targeted changes.

## Local run workflow (SSM connection)
1. Install collections from `collections/requirements.yml`.
2. Ensure AWS credentials are available and `PROJECT_NAME` is set.
3. Ensure the SSM transfer bucket parameter exists and is readable.
4. Run `ansible-playbook site.yml`.

## Pipeline run workflow (CodeBuild)
1. Ensure the CodeStar connection is authorized and the buildspec path is correct.
2. Ensure required non-secret parameters exist in SSM Parameter Store and required secrets exist in Secrets Manager.
3. Confirm the CodeBuild role has S3 access to the SSM transfer bucket.
4. Run the pipeline and inspect the SSM connection phase before role tasks.

## Key files
- `ansible.cfg` defines inventory location and default remote user.
- `inventory/hosts.ini` is the default static inventory.
- `group_vars/all.yml` holds shared variables and SSM or Secrets lookups.
- `site.yml` is the entry-point playbook.
- `roles/core_node/` contains the system and application configuration tasks.
- `scripts/` contains pipeline helpers used by CodeBuild.

## Guardrails
- The `amazon.aws.aws_ssm` connection plugin requires an S3 transfer bucket; configure `ansible_aws_ssm_bucket_name`.
- Inventory success does not imply SSM connection success.
- SSM runs do not honor `remote_user` the same way SSH does; use `become` for privilege.
- DNS-01 automation expects the shared networking assume-role profile on the instance.

## References
- `references/repo-layout.md`
- `references/pipeline-ssm.md`
- `references/secrets-and-params.md`
- `references/dns01-runtime.md`
- `references/inventory-ssm.md`
- `references/group-vars.md`
- `references/role-core-node.md`
- `references/pipeline-scripts.md`
- `references/runbook-certbot-dns01.md`
- `references/runbook-wireguard.md`
- `references/runbook-directus.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arnaringig) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
