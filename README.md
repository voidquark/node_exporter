# Ansible role - Node Exporter

[![License](https://img.shields.io/github/license/voidquark/node_exporter)](LICENSE)

Node Exporter role is designed to simplify the installation and configuration of Node Exporter on various Linux distributions. [Node Exporter](https://github.com/prometheus/node_exporter) is a popular Prometheus exporter that collects hardware and operating system metrics from Linux systems.

This role provides a straightforward way to deploy Node Exporter across systems from the Red Hat Enterprise Linux (RHEL) family, including `RHEL 8`, `RHEL 9`, `Rocky Linux`, `Alma Linux`, `CentOS`, and `Fedora`. It has been tested extensively on `RHEL 8` and `RHEL 9`, ensuring compatibility and reliability.

**Role support two scenarios:**
- **Deployment:** This scenario is idempotent and ensures that Node Exporter is in the required state (`present`). It installs and configures Node Exporter on the target system.
- **Uninstall:** This scenario ensures that everything deployed by the role is removed and cleaned up (`uninstall`). It removes Node Exporter from the target system.

**Role Key feature**:
- **Easy Installation/Uninstall**: The role simplifies the installation and uninstallation processes for Node Exporter, making it quick and hassle-free.
- **Customizable Configuration**: The role provides various configuration options that can be customized according to your needs. This allows you to tailor the deployment of Node Exporter to suit your specific requirements.
- **Version Flexibility**: Allowing users to set the version to either “latest” or a specific version like “1.6.0”. This simple and intuitive feature empowers users to easily control the Node Exporter version according to their deployment requirements.

📢 **[Check the blog post](https://voidquark.com/simplify-node-exporter-deployment-with-ansible/)** 📝 **Experience a step-by-step demo of deploying and uninstalling Node Exporter using my Ansible role.**

## Requirements

No requirements

## Role Variables

- **Default Variables**. Usually, there is no need to change this but rather overwrite the value in `host_vars` or `group_vars` if required.

| Variable Name  | Default Value | Description
| ----------- | ----------- | ----------- |
| `node_exporter_version` | `"latest"` | The version of Node Exporter to download and deploy. Supported standard version "1.6.0" format or "latest".
| `node_exporter_binary_path` | `/usr/local/bin` | The path where the Node Exporter binary is located.
| `node_exporter_user` | `"node_exporter"` | The user under which the Node Exporter service runs. This user is marked as a system user.
| `node_exporter_group` | `"node_exporter"` | The system group associated with the Node Exporter service.
| `node_exporter_web_listen_address` | `"0.0.0.0"` | The address on which Node Exporter listens for HTTP requests. By default, it listens on all interfaces.
| `node_exporter_listen_port` | `9100` | The TCP port on which Node Exporter listens for incoming connections. By default, it listens on port `9100`.
| `node_exporter_expose_port` | `true` | Set to `true` by default, controls whether to add a firewalld rule for exposing the Node Exporter port. When `true`, a firewalld rule is added to allow inbound traffic on the specified Node Exporter port. Set to `false` to ensure that firewalld rule is not present.
| `node_exporter_arch` | `"amd64"` | The architecture of the Linux system for which Node Exporter is being deployed.
| `node_exporter_download_url` | <pre>`https://github.com/`<br>`prometheus/`<br>`node_exporter/releases/`<br>`download/`<br>`v{{ node_exporter_version }}`<br>`/node_exporter-`<br>`{{ node_exporter_version }}`<br>`.linux-`<br>`{{ node_exporter_arch }}.tar.gz`</pre> | The default download URL for the Node Exporter package from GitHub.

- **group_vars** or **host_vars** variables.

| Variable Name | Example Usage | Required | Description
| ----------- | ----------- | ----------- | ----------- |
| `node_exporter_runtime_flags` | <pre>node_exporter_runtime_flags:<br>&emsp;- "--collector.textfile.directory {{ node_exporter_textfile_path }}"<br>&emsp;- "--collector.systemd"<br>&emsp;- "--collector.network_route"</pre> | No | Optional execution flags injected into the systemd unit file for Node Exporter. You can refer to the official [node exporter Github repo](https://github.com/prometheus/node_exporter) to obtain a list of available collectors.
| `node_exporter_textfile_path` | `"/var/lib/node_exporter"` | NO(required when runtime flag textfile is defined) | If you define the `--collector.textfile.directory` flag in the `node_exporter_runtime_flags` variable, it is required to specify the directory path. By default, the directory is not created. The [textfile collector](https://www.robustperception.io/using-the-textfile-collector-from-a-shell-script/#more-4014) serves a specific purpose as explained in the provided link.

## Dependencies

No Dependencies

## Deployment and Uninstall Playbook

- Deployment playbook (`function_node-exporter_deploy.yml`)
```yaml
- name: Deploy node_exporter service
  hosts: node_exporter
  gather_facts: false
  become: true
  roles:
    - role: node_exporter
      state: present
```

- Uninstall playbook (`function_node-exporter_uninstall.yml`)
```yaml
- name: Uninstall node_exporter service
  hosts: node_exporter
  gather_facts: false
  become: true
  roles:
    - role: node_exporter
      state: absent
```

## Example Execution

- Deployment playbook
```yaml
ansible-playbook -i inventory/hosts function_node-exporter_deploy.yml
```

- Uninstall playbook. Ensure that you provided limit to remove only specified host!
```yaml
ansible-playbook -i inventory/hosts function_node-exporter_uninstall.yml --limit "node2.voidquark.com"
```

## Directory Structure Example

Here is an example of how you can organize your files using a directory structure for your Ansible project. This structure demonstrates where to place the playbook (deploy/uninstall), the inventory file, and where to include this role.

```shell
ansible_structure
├── automation
│   ├── function_node-exporter_deploy.yml # Deployment playbook
│   ├── function_node-exporter_uninstall.yml # Uninstall playbook
│   └── roles # Clone this role in to this dir.
│       └── node_exporter
└── inventory
    ├── group_vars
    │   └── node_exporter
    │       └── node_exporter_vars.yml # Overwrite variables in group_vars (optional)
    ├── hosts
    └── host_vars
        ├── node1.voidquark.com
        │   └── host_vars.yml # Overwrite variables in host_vars (optional)
        └── node2.voidquark.com
            └── host_vars.yml # Overwrite variables in host_vars (optional)
```

- inventory `hosts` file example:
```toml
[node_exporter]
node1.voidquark.com
node2.voidquark.com
```

## License

MIT

## Contribution

Feel free to customize and enhance the role according to your needs.
Your feedback and contributions are greatly appreciated. Please open an issue or submit a pull request with any improvements.

## Author Information

Created by [VoidQuark](https://voidquark.com)

