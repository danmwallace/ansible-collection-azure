# TODO

## `azure_arc` role

### Connect step never runs on new hosts (logic bug preserved from source)

`roles/azure_arc/tasks/main.yml` — the `Connect to Azure Arc` task is gated on
`azure_arc_azcmagent_lnx_connected.rc == 0`, but `azcmagent check` returns:

- `rc == 0` when the host is **already connected**
- `rc == 16` when the host is **not connected**

So as written the connect task only fires on hosts that don't need it, and
brand-new hosts never get onboarded. This was carried over verbatim from the
original `ansible_azure_arc` role rather than fixed during migration.

Likely fix: flip the gate to `rc == 16` (and adjust `changed_when` to match).

### Molecule scenario is scaffolding, not a working CI test

`roles/azure_arc/molecule/default/` was created from the standard ansible-native
delegated-driver template. `verify.yml` asserts `/usr/bin/azcmagent` exists,
which means a real run needs:

- the test container to reach `aka.ms` and Microsoft's package endpoints
- the installer to support the container's distro (the default
  `quay.io/centos/centos:stream9` image may not be sufficient)
- the converge step uses placeholder GUIDs for the service principal, so the
  `azcmagent connect` call would fail against a real Azure tenant

Either harden the scenario into something that actually runs end-to-end, or
remove the install assertion and treat it as a syntax/idempotence smoke test
only.

## Collection metadata

`galaxy.yml` still has placeholder values (`authors`, `description`,
`repository`, `documentation`, `homepage`, `issues`) from the
`ansible-galaxy collection init` template. Fill these in before publishing.
