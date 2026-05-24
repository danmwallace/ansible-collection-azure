# danmwallace.azure.azure_arc

Downloads and installs the Azure Connected Machine Agent (`azcmagent`) on a Linux
host, then registers the host with Azure Arc using a service principal. The role
is idempotent: if `azcmagent` is already installed the installer is skipped, and
the connect step only runs after a successful `azcmagent check`.

## Requirements

- Ansible >= 2.16
- Target host running Linux (Debian/Ubuntu and RHEL-family supported; tested on
  Ubuntu 22.04+ and Debian 12).
- Outbound HTTPS access from the target to `aka.ms` and Azure Arc service
  endpoints.
- A service principal with permission to onboard machines into the target
  subscription / resource group.

## Role Variables

All variables are required and have no defaults — supply them via group_vars /
host_vars, typically vault-encrypted.

| Variable                          | Type | Description                                                              |
|-----------------------------------|------|--------------------------------------------------------------------------|
| `azure.service_principal_id`      | str  | Application (client) ID of the onboarding service principal. `no_log`.   |
| `azure.service_principal_secret`  | str  | Client secret of the onboarding service principal. `no_log`.             |
| `azure.tenant_id`                 | str  | Azure AD tenant ID that owns the subscription.                           |
| `azure.subscription_id`           | str  | Subscription ID where the Arc-enabled server resource will be created.   |
| `azure.resource_group`            | str  | Resource group that will contain the Arc-enabled server resource.        |
| `azure.location`                  | str  | Azure region for the Arc-enabled server resource (e.g. `eastus`).        |

See `meta/argument_specs.yml` for the authoritative interface.

## Dependencies

None.

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
