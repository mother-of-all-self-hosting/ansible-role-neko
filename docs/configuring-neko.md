<!--
SPDX-FileCopyrightText: 2020 - 2024 MDAD project contributors
SPDX-FileCopyrightText: 2020 - 2024 Slavi Pantaleev
SPDX-FileCopyrightText: 2020 Aaron Raimist
SPDX-FileCopyrightText: 2020 Chris van Dijk
SPDX-FileCopyrightText: 2020 Dominik Zajac
SPDX-FileCopyrightText: 2020 Mickaël Cornière
SPDX-FileCopyrightText: 2022 François Darveau
SPDX-FileCopyrightText: 2022 Julian Foad
SPDX-FileCopyrightText: 2022 Warren Bailey
SPDX-FileCopyrightText: 2023 Antonis Christofides
SPDX-FileCopyrightText: 2023 Felix Stupp
SPDX-FileCopyrightText: 2023 Pierre 'McFly' Marty
SPDX-FileCopyrightText: 2024 - 2025 Suguru Hirahara

SPDX-License-Identifier: AGPL-3.0-or-later
-->

# Setting up Neko

This is an [Ansible](https://www.ansible.com/) role which installs [Neko](https://neko.m1k1o.net/) to run as a [Docker](https://www.docker.com/) container wrapped in a systemd service.

Neko is a self-hosted virtual browser which runs in a Docker's container based on the WebRTC protocol. It enables to run not only web browsers but also desktop environments like KDE and Xfce remotely.

See the project's [documentation](https://neko.m1k1o.net/docs/v3/introduction) to learn what Neko does and why it might be useful to you.

✨ Neko (ねこ/猫) means cat 🐱 in Japanese.

## Prerequisites

To use the service, by default you need to open ports `56000-56100/udp` of the host. See [this section](https://neko.m1k1o.net/docs/v3/configuration/webrtc#epr) on the official documentation for details.

## Adjusting the playbook configuration

To enable Neko with this role, add the following configuration to your `vars.yml` file.

**Note**: the path should be something like `inventory/host_vars/mash.example.com/vars.yml` if you use the [MASH Ansible playbook](https://github.com/mother-of-all-self-hosting/mash-playbook).

```yaml
########################################################################
#                                                                      #
# neko                                                                 #
#                                                                      #
########################################################################

neko_enabled: true

########################################################################
#                                                                      #
# /neko                                                                #
#                                                                      #
########################################################################
```

### Set the hostname

To enable the Neko instance you need to set the hostname as well. To do so, add the following configuration to your `vars.yml` file. Make sure to replace `example.com` with your own value.

```yaml
neko_hostname: "example.com"
```

After adjusting the hostname, make sure to adjust your DNS records to point the domain to your server.

**Note**: hosting Neko under a subpath (by configuring the `neko_path_prefix` variable) does not seem to be possible due to Neko's technical limitations.

### Configuring authentication

#### Set password for admin and regular users

By default the instance is configured to authenticate administrators and regular users with passwords.

To specify the passwords, add the following configuration to your `vars.yml` file. Make sure to replace `ADMIN_PASSWORD_HERE` and `USER_PASSWORD_HERE` with your own values.

```yaml
neko_environment_variables_neko_member_multiuser_admin_password: ADMIN_PASSWORD_HERE

neko_environment_variables_neko_member_multiuser_user_password: USER_PASSWORD_HERE
```

#### Use another authentication method (optional)

You can change the authentication method by setting `neko_environment_variables_neko_member_provider`. See [this section](https://neko.m1k1o.net/docs/v3/configuration/authentication#member) on the official documentation for the available methods.

### Select an application (optional)

There are multiple applications available to use on the Neko's instance. For example, by adding the following configuration to your `vars.yml` file you can set up KDE desktop environment in a container where you can run multiple applications:

```yaml
neko_container_image_app: "kde"
```

By default, the instance is set to use Firefox as the application.

See [this page](https://neko.m1k1o.net/docs/v3/installation/docker-images#apps) of the official documentation for the list of available applications.

>[!WARNING]
>
> Setting a Chromium-based browser modifies shared memory size to 2 GB and enables `--cap-add=SYS_ADMIN`. See [this page](https://www.redhat.com/en/blog/container-tidbits-adding-capabilities-container) for the security implication of enabling it.

### Extending the configuration

There are some additional things you may wish to configure about the component.

Take a look at:

- [`defaults/main.yml`](../defaults/main.yml) for some variables that you can customize via your `vars.yml` file. You can override settings (even those that don't have dedicated playbook variables) using the `neko_environment_variables_additional_variables` variable

For a complete list of Neko's config options that you could put in `neko_environment_variables_additional_variables`, see its [environment variables](https://neko.m1k1o.net/docs/v3/configuration).

## Installing

After configuring the playbook, run the installation command of your playbook as below:

```sh
ansible-playbook -i inventory/hosts setup.yml --tags=setup-all,start
```

If you use the MASH playbook, the shortcut commands with the [`just` program](https://github.com/mother-of-all-self-hosting/mash-playbook/blob/main/docs/just.md) are also available: `just install-all` or `just setup-all`

## Usage

After running the command for installation, Neko becomes available at the specified hostname like `https://example.com`.

To get started, go to the URL on a web browser and log in to the instance with the specified authentication method. By default you can log in as a regular user or administrator with passwords. **Note that the display name is not a username to be used with the password for logging in.**

After logging in, you can start using the virtual browser by toggling the switch at the bottom of the UI to gain control of it. Without the control, it will not respond to an input with a mouse or keyboard.

## Troubleshooting

If you cannot log in to the instance, make sure that the ports are open on the server and WebRTC is enabled on the browser.

### Check the service's logs

You can find the logs in [systemd-journald](https://www.freedesktop.org/software/systemd/man/systemd-journald.service.html) by logging in to the server with SSH and running `journalctl -fu neko` (or how you/your playbook named the service, e.g. `mash-neko`).
