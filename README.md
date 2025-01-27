# Proxmox Cloud-init

The [template](template) script assists with the creation of Proxmox templates using cloud-based images.

## ⚙️ Prerequisites

### Configure Storage

Custom cloud-init configuration files must be stored on a storage that supports "snippets" and needs to be available on all nodes that the virtual machine may be migrated to. The script uses the `local` storage for storing cloud-init configuration files.

Edit the `local` datacenter storage by **Datacenter** > **Storage** > `local` > **Edit**. Add `Snippets` to the **Content** list, then click **OK**.

### Install the Script

Copy the script onto the Proxmox instance using your preferred method. For example

```shell
scp template root@IP_ADDRESS:~
```

> [!TIP]
> The script should be executable which can be achieved with `chmod +x template`

### Create Cloud-init Configuration

Create the custom cloud-init configuration file `user-data.yaml` alongside the script. An example of such content is

```yaml
#cloud-config

timezone: Australia/Brisbane

users:
  - default

user: # overrides the default user
  name: proxmox
  gecos: Proxmox User
  lock_passwd: false
  passwd: $6$LMpbedEG$pP1QpD6dzfljkWUwj5l9C8k1
  ssh_authorized_keys:
    - ssh-ed25519 AAAACxG/xsUyz7Zut6L6 proxmox

chpasswd:
  expire: False

disable_root: true
disable_root_opts: ''

package_update: true
package_upgrade: true
package_reboot_if_required: true
packages:
  - python3
  - python3-pip
  - qemu-guest-agent

power_state: # reboot after QEMU guest agent install
  mode: reboot
```

Refer to the [cloud-init modules](https://cloudinit.readthedocs.io/en/latest/reference/modules.html) documentation for more information.

## 📝 Create a Template

Create a new template

```shell
./template IMAGE_URL
```

> [!TIP]
> Additional script options can be reviewed with `./template --help`

## 🚀 Consume a Template

Select **Clone** on the template. Set the **Mode** as **Full Clone**, a **Name** and **Target Storage**, and then select **Clone**.

> [!WARNING]
> Resize the hard disk by **Hardware** > **Hard Disk** > **Disk Action** > **Resize**. Set the **Size Increment** as desired, then select **Resize disk**
