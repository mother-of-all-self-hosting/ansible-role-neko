# neko Ansible Role

![neko Logo](assets/logo.png)

This app uses Web RTC to stream a desktop inside of a docker container. This role helps you to set up neko:

- with everything run in [Docker](https://www.docker.com/) containers
- powered by [the official neko container image](https://hub.docker.com/r/m1k1o/neko/)


## Installing

To configure and install neko on your own server(s), you should use a playbook like [Mother of all self-hosting](https://github.com/mother-of-all-self-hosting/mash-playbook) or write your own.

# Configuring this role for your playbook

```
neko_enabled: true
neko_hostname: 'example.org'
neko_password: ''
neko_password_admin: ''
```