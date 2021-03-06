# Ansible deploy

![Syntax Check](https://github.com/bingzhangdai/ansible-deploy/workflows/Syntax%20Check/badge.svg?branch=master&event=push)

Configure systems and deploy software by using Ansible.

## Prerequisites

* control machine
  * python and pip installed. (python3 is preferred)
* remote server
  * python interpreter present (most popular distributions already satisfy this condition)

If you are using Windows, it is strongly recommended to use Windows Subsystem for Linux ([WSL docs](https://docs.microsoft.com/en-us/windows/wsl)).

## This playbook can

* install and configure common softwares like bash, vim, etc. ([How-to](roles/common/README.md))
  * tested platforms:
    * Ubuntu 18.04+
    * CentOS 7.5
* install latest docker-ce
  * tested platforms:
    * Ubuntu 18.04+
    * CentOS 7.5
* install shadowsocks-libev and optionally v2ray-plugin ([How-to](roles/shadowsocks-libev/README.md))
  * tested platforms:
    * Ubuntu 18.04+

## How to use

### Clone and config environment

```bash
git clone https://github.com/bingzhangdai/ansible-deploy.git
cd ansible-deploy
# sudo make env PYTHON=python3
sudo make evn
```

If you are using Windows, it is recommended to change the line ending before playing.

```bash
git ls-files | xargs dos2unix
```

### Edit inventory (hosts)

Modify hosts and put all remote servers in file `hosts`.

```ini
host_or_ip_0
host_or_ip_1
```

### Modify default group vars

Update vars under `group_vars/all/*`. Change remote user name and password for variable `ansible_user` and `ansible_ssh_pass` in file `group_vars/all/main.yml` for ssh login.

```yml
ansible_user: root
ansible_ssh_pass: root_passwd
```

Modify other vars as needed.

### Encrypt password (optional)

Use ansible-vault to encrypt password, `make encrypt`.

### Installation

For full installation, run the following command.

```bash
ansible-playbook site.yml -i hosts
```

To install or skip one or more components. Just look at the file `site.yml` and decide.

```bash
# install only shadowsocks-libev
ansible-playbook site.yml -i hosts --tags "shadowsocks-libev"
# install common packages but do not copy config files
ansible-playbook site.yml -i hosts --tags "common" --skip-tags "configuration"
```

If you see the warning `[WARNING]: Ansible is being run in a world writable directory`, you can export `ANSIBLE_CONFIG` environmental variable.

```bash
export ANSIBLE_CONFIG=`pwd`/ansible.cfg
```

Enjoy!

## Known issues

Ansible known issues

* [Synchronize with password authentication does not work with python 2](https://github.com/ansible/ansible/issues/56629): `[Errno 32] Broken pipe`.

  Workaround: using python3
* [Synchronize module doesn't work with vault](https://github.com/ansible/ansible/issues/45161)

  If you are using ansible-vault, then a workaround is adding argument `--extra-vars 'ansible_ssh_pass=your_passwd'` in `ansible-playbook` command
