# range-playbooks

A collection of Ansible playbooks designed for use with [the range](https://github.com/cofcsecurity/cyber-range).

## Playbooks

### Blue

Blue team oriented playbooks. (ie setup auditd, ufw)

| Playbook | Description | Valid Targets |
| -------- | ----------- | ------------- |
| auditd-setup | Installs and enables auditd | Debian based Linux |

### Gold

Range administration oriented playbooks. (ie configure default users, services)

### Red

Red team oriented playbooks. (ie install additional persisitance mechanisims)

| Playbook | Description | Valid Targets |
| -------- | ----------- | ------------- |
| sudo-wordle | Configures pam-wordle as a required authentication module for sudo | Debian based Linux |

## Ansible

### Authentication

If running outside of the range environment authentication must be configured for both the bastion jumpbox and the internal hosts.

SSH key based authentication must be used for proxying through the bastion server. 
The easiest way to accomplish this is to add the SSH key to your authentication agent using ssh-add.

```
ssh-add range.pem
ansible all -m ping
```

Passwords or SSH keys can be used to connect to internal hosts. The sshpass package may be needed to use passwords.

```
apt install sshpass
```

To disable host key checking:

```
export ANSIBLE_HOST_KEY_CHECKING=False
```

### Sample Hosts File (/etc/ansible/hosts)

```yaml
all:
  hosts:
    # Auth via SSH key using ssh-add prior to running any playbooks
    bastion:
      ansible_host: set-me
      ansible_user: ubuntu
  children:
    blue_linux:
      hosts:
        one:
          ansible_host: 10.0.10.10
        two:
          ansible_host: 10.0.10.20
      vars:
        ansible_user: gold
        ansible_password: range
        ansible_sudo_pass: range
        ansible_ssh_common_args: '-o StrictHostKeyChecking=no -o ProxyCommand="ssh -W %h:%p -q ubuntu@set-me"'
```
