# Windows Template Build

This role automates converting a fresh Windows installation into a reusable template. It can be executed against virtual machines running on VMware, oVirt/RHV, EC2, Azure and other platforms. Typical tasks include installing drivers, applying Windows updates, enabling RDP, installing Cloudbase-Init and finally running sysprep.

> **Note:** The tasks provided here are meant as examples. Review and adapt them to match your environment and security policies before using in production.

## Requirements

* Ansible **2.9** or newer
* A reachable Windows VM with WinRM enabled
* Administrative credentials for the VM
* Internet access for downloading updates and tools

If WinRM is not yet configured on the guest, execute [`ConfigureRemotingForAnsible.ps1`](https://raw.githubusercontent.com/ansible/ansible-documentation/devel/examples/scripts/ConfigureRemotingForAnsible.ps1) from the Ansible project. The URL of this script can be changed via the `winrm_enable_script_url` variable.

## Role Variables

Below are some frequently adjusted variables. See `defaults/main.yml` and `vars/main.yml` for the complete list.

| Variable | Default | Purpose |
|----------|---------|---------|
| `install_updates` | `true` | Apply Windows updates |
| `remove_apps` | `false` | Remove built‑in applications |
| `upgrade_powershell` | `false` | Install a newer PowerShell version |
| `target_ovirt` | `false` | Install oVirt/QEMU drivers |
| `target_ec2` | `false` | Install the AWS ENA driver |
| `target_vagrant` | `false` | Settings for Vagrant boxes |
| `shutdown_instance` | `true` | Power off after sysprep |

Local administrator credentials and additional user accounts are defined in `vars/main.yml`.

## Dependencies

The role depends on several other roles published on Ansible Galaxy:

- oatakan.windows_ec2_ena_driver
- oatakan.windows_powershell_upgrade
- oatakan.windows_configure_update
- oatakan.windows_update
- oatakan.windows_virtio
- oatakan.windows_vmware_tools
- oatakan.windows_virtualbox_guest_additions
- oatakan.windows_parallels_tools
- oatakan.windows_hotfix

Install them with `ansible-galaxy install -r requirements.yml` or using `ansible-galaxy collection install`.

## Example Playbook

Inventory example:

```ini
[windows]
winhost ansible_host=192.0.2.10 ansible_user=Administrator ansible_password=Secret123
```

Playbook example:

```yaml
- name: Build Windows template
  hosts: windows
  vars:
    ansible_connection: winrm
    ansible_port: 5986
    ansible_winrm_transport: credssp
    ansible_winrm_server_cert_validation: ignore
  roles:
    - oatakan.windows_template_build
```

Run the playbook with:

```bash
ansible-playbook -i inventory build.yml
```

Once the play completes, the virtual machine shuts down and can be used to create new templates on your virtualization platform.

## License

MIT

## Author

Orcun Atakan
