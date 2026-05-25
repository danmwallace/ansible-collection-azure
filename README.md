# Ansible Collection — danmwallace.azure

Roles for Azure Arc and related cloud-native integrations that connect homelab
and on-prem Linux hosts to Azure. The collection's focus is hybrid management:
onboarding existing machines into Azure so they can be governed, monitored, and
inventoried from the cloud control plane rather than provisioning new Azure
resources. Connection secrets (service principal credentials) are expected to
come from Ansible Vault.

## Requirements

- Ansible >= 2.16 (highest `min_ansible_version` across the collection's roles)
- No collection dependencies (`galaxy.yml` declares `dependencies: {}`)
- Target hosts running Linux. The `azure_arc` role supports Debian (bookworm),
  Ubuntu (jammy, noble), Fedora (40, 41), and EL 9, and needs outbound HTTPS to
  `aka.ms` and the Azure Arc service endpoints.
- An Azure service principal with permission to onboard machines into the target
  subscription and resource group.

## Installation

```bash
ansible-galaxy collection install danmwallace.azure
```

Or pin it in `requirements.yml`:

```yaml
collections:
  - name: danmwallace.azure
    version: ">=1.0.0"
```

## Roles

| Role | Description |
| --- | --- |
| [`danmwallace.azure.azure_arc`](roles/azure_arc/README.md) | Install and connect the Azure Connected Machine Agent (Azure Arc) on Linux hosts. |

## Example Playbook

```yaml
- name: Onboard Linux hosts to Azure Arc
  hosts: arc_targets
  become: true
  roles:
    - role: danmwallace.azure.azure_arc
      vars:
        azure:
          service_principal_id: "{{ vault_arc_sp_id }}"
          service_principal_secret: "{{ vault_arc_sp_secret }}"
          tenant_id: "{{ vault_arc_tenant_id }}"
          subscription_id: "{{ vault_arc_subscription_id }}"
          resource_group: "rg-arc-prod"
          location: "eastus"
```

## License

MIT
