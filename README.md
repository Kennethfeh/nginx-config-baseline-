# Nginx Config Baseline

An opinionated Ansible project I use to prove configuration management chops: it provisions nginx, deploys templated content, and hardens basics while staying idempotent.

## Layout

| Path | Description |
| --- | --- |
| `ansible/` | Inventory example, site playbook, and the `webserver` role. |
| `ansible/roles/webserver/tasks/main.yml` | Installs nginx via apt, deploys a templated homepage, and ensures the service is enabled. |
| `ansible/roles/webserver/templates/index.html.j2` | Human-friendly landing page that prints deploy timestamps using Ansible facts. |
| `ansible.cfg` | Sets default inventory path and roles directory for local development.

## Running the playbook

```bash
cd nginx-config-baseline
cp ansible/inventory.example ansible/inventory
ansible-galaxy collection install ansible.posix community.general
ansible-playbook -i ansible/inventory ansible/playbooks/site.yml
```

Override hosts or ssh keys in the copied inventory. The playbook targets the `webservers` group and uses privilege escalation to manage nginx.

## CI guardrails

The `nginx_config_baseline` job in `.github/workflows/portfolio.yml` installs Ansible + ansible-lint, runs `ansible-lint ansible`, and executes `ansible-playbook --syntax-check`. That mirrors the controls I add before giving infra repos to junior engineers.

## Extending the baseline

- Add TLS configuration by dropping extra templates + handlers.
- Register Prometheus node exporter or Datadog agents in follow-up roles.
- Replace the static HTML with a templated config pulled from Vault/Consul—`index.html.j2` already demonstrates how to interpolate facts.
