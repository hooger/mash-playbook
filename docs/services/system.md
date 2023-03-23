# System-related configuration

This Ansible playbook can install and configure various system-related things for you.
All the sections below relate to the host OS instead of the managed containers.

### swap

To enable [swap](https://en.wikipedia.org/wiki/Memory_paging) management (also read more in the [Swap](https://wiki.archlinux.org/title/Swap) article in the [Arch Linux Wiki](https://wiki.archlinux.org/)), add the following configuration to your `vars.yml` file and re-run the [installation](../installing.md) process:

```yaml
########################################################################
#                                                                      #
# system                                                               #
#                                                                      #
########################################################################

system_swap_enabled: true

########################################################################
#                                                                      #
# /system                                                              #
#                                                                      #
########################################################################
```

A swap file will be created in `/var/swap` (configured using the `system_swap_path` variable) and enabled in your `/etc/fstab` file.

By default, the swap file will have the following size:

- on systems with `<= 2GB` of RAM, swap file size = `total RAM * 2`
- on systems with `> 2GB` of RAM, swap file size = `1GB`

To avoid these calculations and set your own size explicitly, set the `system_swap_size` variable in megabytes, example (4gb):

```yaml
system_swap_size: 4096
```

**Warning**: changing `system_swap_size` subsequently will not recreate the SWAP file with the new size. You will need to disable swap, re-run the playbook (to make it clean up), then enable it again with the new size.

### ssh

> **Warning**: advanced functionality! While the default config with a few adjustments was battle tested on hundreds of servers,
> you should use it with caution and verify everything before you apply the changes!

To enable [ssh server](https://www.openssh.com/) config and authorized/unauthorized keys management, add the following configuration to your `vars.yml` file and re-run the [installation](../installing.md) process:

```yaml
########################################################################
#                                                                      #
# system                                                               #
#                                                                      #
########################################################################

system_security_ssh_enabled: true

system_security_ssh_port: 22

system_security_ssh_authorizedkeys: [] # list of authorized public keys
system_security_ssh_unauthorizedkeys: [] # list of unauthorized/revoked public keys

########################################################################
#                                                                      #
# /system                                                              #
#                                                                      #
########################################################################
```

The [default configuration](https://gitlab.com/etke.cc/roles/ssh/-/blob/main/defaults/main.yml) is good enough as-is, but we strongly suggest you to **verify everything before applying any changes!**, otherwise you may lock yourself out of the server.

With this configuration, the default `/etc/ssh/sshd_config` file on your server will be replaced by a new one, managed by the [ssh role](https://gitlab.com/etke.cc/roles/ssh) (see its [templates/etc/ssh/sshd_config.j2](https://gitlab.com/etke.cc/roles/ssh/-/blob/main/templates/etc/ssh/sshd_config.j2) file).

There are various configuration options - check the defaults and adjust them to your needs.

### fail2ban

To enable [fail2ban](https://fail2ban.org/wiki/index.php/Main_Page) installation, management and integration with SSHd, add the following configuration to your `vars.yml` file and re-run the [installation](../installing.md) process:

```yaml
########################################################################
#                                                                      #
# system                                                               #
#                                                                      #
########################################################################

system_security_fail2ban_enabled: true

system_security_fail2ban_sshd_port: 22
# If you enabled playbook-managed ssh as described above,
# you can replace the line above with the following:
# system_security_fail2ban_sshd_port: "{{ system_security_ssh_port }}"

########################################################################
#                                                                      #
# /system                                                              #
#                                                                      #
########################################################################
```