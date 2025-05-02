
#  Ansible Workshop  

## 0 · Lab Prep (5 min)

### Your VM
| Hostname | IP | User | Password |
|---|---|---|---|
| **workshop‑vm** | `192.168.56.20` | `workshop` | `P@ssw0rd!` |

**Verify access**

```bash
ssh workshop@192.168.56.20
# 1. Create the account and its home directory
useradd -m -s /bin/bash devops

# 2. Set an initial password (you'll be prompted to type it twice)
passwd devops

# 3. Add the user to the wheel group
usermod -aG wheel devops

# 4. (Optional) Make wheel group password‑less
#    Uncomment or add this line in /etc/sudoers:
#    devops ALL=(ALL) NOPASSWD: ALL
visudo

```

### Install Ansible on your laptop (control node)

```diff 
## ubuntu
$ sudo apt update
$ sudo apt install software-properties-common
$ sudo add-apt-repository --yes --update ppa:ansible/ansible
$ sudo apt install ansible

 ansible --version
```

```yaml 
 https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html#installing-distros

```
*** to genrate config file ***
```diff 
# Since Ansible 2.12 (core):
# To generate an example config file (a "disabled" one with all default settings, commented out):
#               $ ansible-config init --disabled > ansible.cfg
#
# Also you can now have a more complete file by including existing plugins:
# ansible-config init --disabled -t all > ansible.cfg

# For previous versions of Ansible you can check for examples in the 'stable' branches of each version
# Note that this file was always incomplete  and lagging changes to configuration settings

# for example, for 2.9: https://github.com/ansible/ansible/blob/stable-2.9/examples/ansible.cfg

```

```diff 
### Minimal inventory

`inventory.ini`

```ini
[servers]
workshop-vm ansible_host=192.168.56.20

[servers:vars]
ansible_user=workshop
ansible_password=P@ssw0rd!
ansible_python_interpreter=/usr/bin/python3
```

***best practice ***
```diff 
ssh-keygen -t ed25519 -f ~/.ssh/workshop -N ''
ssh-copy-id -i ~/.ssh/workshop.pub cloud_user@63.32.88.169
```


```bash
export ANSIBLE_HOST_KEY_CHECKING=False
```

---



## 1 · Hello Ansible

### 1.1 Ping the host

```bash
ansible -i inventory.ini servers -m ansible.builtin.ping
```

### 1.2 Gather basic facts

```bash
ansible -i inventory.ini servers -m ansible.builtin.setup -a 'filter=ansible_distribution*'
```

```diff 
ansible -i inventory.ini servers -m ansible.builtin.command -a 'whoami' -b
```

---

## 2 · Prep Playbook

`prep.yml`

```yaml
---
- name: Prepare single host with common tools
  hosts: servers
  become: true
  vars:
    common_packages:
      - curl
      - htop
      - wget
    remove_packages:
      - nano           
      - apache2
      - htop
  tasks:


    - name: Remove unwanted packages
      ansible.builtin.apt:
        name: "{{ remove_packages }}"
        state: absent
        purge: yes
      when: remove_packages | length > 0

    - name: Install common packages
      ansible.builtin.apt:
        name: "{{ common_packages }}"
        state: present

- name: Prepare single host with common tools 2
  hosts: servers2
  become: true
  vars:
    common_packages:
      - curl
      - htop
      - wget
    remove_packages:
      - nano           
      - apache2
      - htop
  tasks:

    - name: Remove unwanted packages
      ansible.builtin.apt:
        name: "{{ remove_packages }}"
        state: absent
        purge: yes
      when: remove_packages | length > 0

    - name: Install common packages
      ansible.builtin.apt:
        name: "{{ common_packages }}"
        state: present
      when:  ansible_facts['os_family'] == 'Debian'

```

Run:

```bash
ansible-playbook -i inventory.ini prep.yml
```
