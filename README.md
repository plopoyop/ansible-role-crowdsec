# plopoyop.crowdsec

> [!WARNING]
> **This role is deprecated and this repository is archived.** It now lives in the
> [`plopoyop.personal_network_gateway`](https://github.com/plopoyop/ansible-collection-personal_network_gateway)
> collection as `plopoyop.personal_network_gateway.crowdsec`, with the same variables:
>
> ```shell
> ansible-galaxy collection install plopoyop.personal_network_gateway
> ```
>
> Then replace `plopoyop.crowdsec` with `plopoyop.personal_network_gateway.crowdsec` in your playbooks.

Install & configure CrowdSec and its firewall bouncer from the official repository

## Table of contents

- [Requirements](#requirements)
- [Default Variables](#default-variables)
  - [crowdsec_acquisitions](#crowdsec_acquisitions)
  - [crowdsec_collections](#crowdsec_collections)
  - [crowdsec_config](#crowdsec_config)
  - [crowdsec_console_enrollment_key](#crowdsec_console_enrollment_key)
  - [crowdsec_console_instance_name](#crowdsec_console_instance_name)
  - [crowdsec_firewall_bouncer_config](#crowdsec_firewall_bouncer_config)
  - [crowdsec_firewall_bouncer_enabled](#crowdsec_firewall_bouncer_enabled)
  - [crowdsec_firewall_bouncer_package](#crowdsec_firewall_bouncer_package)
  - [crowdsec_firewall_bouncer_restart_with](#crowdsec_firewall_bouncer_restart_with)
  - [crowdsec_package_state](#crowdsec_package_state)
  - [crowdsec_parsers](#crowdsec_parsers)
  - [crowdsec_repository_distribution](#crowdsec_repository_distribution)
  - [crowdsec_repository_suite](#crowdsec_repository_suite)
  - [crowdsec_scenarios](#crowdsec_scenarios)
  - [crowdsec_service_enabled](#crowdsec_service_enabled)
  - [crowdsec_service_state](#crowdsec_service_state)
  - [crowdsec_whitelist_cidrs](#crowdsec_whitelist_cidrs)
  - [crowdsec_whitelist_ips](#crowdsec_whitelist_ips)
- [Dependencies](#dependencies)
- [License](#license)
- [Author](#author)

---

## Requirements

- Minimum Ansible version: `2.19`

## Default Variables

### crowdsec_acquisitions

Log sources, one file /etc/crowdsec/acquis.d/<name>.yaml each.
`config` is the CrowdSec acquisition document.

**_Type:_** list<br />

#### Default value

```YAML
crowdsec_acquisitions: []
```

#### Example usage

```YAML
crowdsec_acquisitions:
  - name: "caddy"
    config:
      filenames: ["/var/log/caddy/*.log"]
      labels:
        type: "caddy"
```

### crowdsec_collections

Hub collections to install (never removed by the role)

**_Type:_** list<br />

#### Default value

```YAML
crowdsec_collections:
  - crowdsecurity/linux
```

#### Example usage

```YAML
crowdsec_collections:
  - "crowdsecurity/linux"
  - "crowdsecurity/caddy"
  - "crowdsecurity/base-http-scenarios"
  - "crowdsecurity/http-cve"
```

### crowdsec_config

Settings merged by CrowdSec over /etc/crowdsec/config.yaml, written to
/etc/crowdsec/config.yaml.local (removed when empty)

**_Type:_** dict<br />

#### Default value

```YAML
crowdsec_config: {}
```

### crowdsec_console_enrollment_key

Enrollment key of app.crowdsec.net (optional; keep it in a vault). Enrollment
runs once, then must be accepted in the console.

**_Type:_** string<br />

#### Default value

```YAML
crowdsec_console_enrollment_key: ''
```

### crowdsec_console_instance_name

Name of this engine in the CrowdSec console

**_Type:_** string<br />

#### Default value

```YAML
crowdsec_console_instance_name: '{{ inventory_hostname }}'
```

### crowdsec_firewall_bouncer_config

Settings merged over the bouncer configuration, written to
/etc/crowdsec/bouncers/crowdsec-firewall-bouncer.yaml.local (removed when empty)

**_Type:_** dict<br />

#### Default value

```YAML
crowdsec_firewall_bouncer_config: {}
```

### crowdsec_firewall_bouncer_enabled

Install the firewall bouncer, registered against the local API by its package.
Setting it back to false does not uninstall an already installed bouncer.

**_Type:_** boolean<br />

#### Default value

```YAML
crowdsec_firewall_bouncer_enabled: true
```

### crowdsec_firewall_bouncer_package

Firewall bouncer flavour: crowdsec-firewall-bouncer-nftables or
crowdsec-firewall-bouncer-iptables

**_Type:_** string<br />

#### Default value

```YAML
crowdsec_firewall_bouncer_package: crowdsec-firewall-bouncer-nftables
```

### crowdsec_firewall_bouncer_restart_with

Firewall unit whose reload/restart wipes the bouncer tables (a ruleset
starting with `flush ruleset`, e.g. "nftables.service"). The bouncer is then
restarted with it through systemd drop-ins. Empty: no drop-ins.

**_Type:_** string<br />

#### Default value

```YAML
crowdsec_firewall_bouncer_restart_with: ''
```

### crowdsec_package_state

State of the CrowdSec packages: "present" (upgrades left to apt /
unattended-upgrades) or "latest" (upgrade on every run)

**_Type:_** string<br />

#### Default value

```YAML
crowdsec_package_state: present
```

### crowdsec_parsers

Extra hub parsers to install (never removed by the role)

**_Type:_** list<br />

#### Default value

```YAML
crowdsec_parsers: []
```

### crowdsec_repository_distribution

Distribution path of the official CrowdSec apt repository (debian or ubuntu)

**_Type:_** string<br />

#### Default value

```YAML
crowdsec_repository_distribution: "{{ ansible_facts['distribution'] | lower }}"
```

### crowdsec_repository_suite

Suite of the official CrowdSec apt repository. Defaults to the host
release, except releases not published yet by CrowdSec (see
crowdsec_repository_suite_fallback in vars/main.yml).

**_Type:_** string<br />

#### Default value

```YAML
crowdsec_repository_suite: >-
  {{ crowdsec_repository_suite_fallback.get(ansible_facts['distribution_release'],
  ansible_facts['distribution_release']) }}
```

### crowdsec_scenarios

Extra hub scenarios to install (never removed by the role)

**_Type:_** list<br />

#### Default value

```YAML
crowdsec_scenarios: []
```

### crowdsec_service_enabled

Enable the CrowdSec and firewall bouncer services on boot

**_Type:_** boolean<br />

#### Default value

```YAML
crowdsec_service_enabled: true
```

### crowdsec_service_state

State of the CrowdSec and firewall bouncer services

**_Type:_** string<br />

#### Default value

```YAML
crowdsec_service_state: started
```

### crowdsec_whitelist_cidrs

Networks never banned (same mechanisms as crowdsec_whitelist_ips),
e.g. the LAN and VPN

**_Type:_** list<br />

#### Default value

```YAML
crowdsec_whitelist_cidrs: []
```

### crowdsec_whitelist_ips

IP addresses never banned: local whitelist parser (alerts from logs) and
CrowdSec allowlist "ansible" (also filters blocklists and manual decisions)

**_Type:_** list<br />

#### Default value

```YAML
crowdsec_whitelist_ips: []
```

## Dependencies

None.

## License

MPL-2.0

## Author

Clément Hubert
