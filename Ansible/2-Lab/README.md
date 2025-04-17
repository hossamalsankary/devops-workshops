
---

## 2 · Deploy a Simple Web Page

`web.yml`

```yaml
---
- name: Run NGINX and custom landing page
  hosts: servers
  become: yes
  vars:
    page_title: "Hello from Ansible!"
  tasks:
    - name: Ensure nginx is installed
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Deploy index.html
      ansible.builtin.copy:
        dest: /var/www/html/index.html
        content: "<h1>{{ page_title }}</h1>"
        owner: www-data
        mode: "0644"
      notify: restart nginx

  handlers:
    - name: restart nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```

Run & test:

```bash
ansible-playbook -i inventory.ini web.yml
curl http://192.168.56.20
```

---

## 4 · (Option) Dockerised App

```bash
ansible -i inventory.ini servers \
  -m community.docker.docker_container \
  -a 'name=hello image=nginx:alpine published_ports="8080:80" state=started' \
  -b
```

Visit `http://192.168.56.20:8080`.

---

## 5 · Extra Mini‑Labs

### 5.1 Manage Users & Groups

`users.yml`

```yaml
---
- name: Create a deploy user and sudo group
  hosts: servers
  become: yes
  tasks:
    - name: Ensure group exists
      ansible.builtin.group:
        name: deploy
        state: present

    - name: Ensure user exists and is part of sudo & deploy
      ansible.builtin.user:
        name: deploy
        groups: "deploy,sudo"
        append: yes
        create_home: yes
        shell: /bin/bash
```

### 5.2 Hardening SSH

`ssh_hardening.yml`

```yaml
---
- name: Disable root SSH login
  hosts: servers
  become: yes
  tasks:
    - name: Set PermitRootLogin to no
      ansible.builtin.lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^#?PermitRootLogin'
        line: 'PermitRootLogin no'
        state: present
      notify: restart sshd

  handlers:
    - name: restart sshd
      ansible.builtin.service:
        name: ssh
        state: restarted
```

### 5.3 Schedule Automatic Updates

`cron.yml`

```yaml
---
- name: Nightly unattended-upgrades
  hosts: servers
  become: yes
  tasks:
    - name: Install unattended-upgrades
      ansible.builtin.apt:
        name: unattended-upgrades
        state: present

    - name: Add cron job
      ansible.builtin.cron:
        name: 'auto-updates'
        user: root
        job: '/usr/bin/unattended-upgrade -d'
        hour: 2
        minute: 0
```

### 5.4 Audit Package Versions

`audit.yml`

```yaml
---
- name: Capture nginx version
  hosts: servers
  tasks:
    - name: Get version
      ansible.builtin.command: nginx -v
      register: nginx_ver
      ignore_errors: yes

    - name: Show result
      ansible.builtin.debug:
        msg: "Nginx version output: {{ nginx_ver.stderr }}"
```

### 5.5 Encrypt Secrets with Vault

```bash
ansible-vault create group_vars/servers/vault.yml
```

Inside `vault.yml`:

```yaml
db_password: "Sup3rSecret!"
```

Use in a playbook:

```yaml
vars_files:
  - vault.yml
```

### 5.6 OS‑Aware Package Installs (htop · Docker · NGINX)

See `htop.yml`, `docker.yml`, `nginx.yml` in this folder for OS‑aware examples that choose `apt` or `yum` based on `ansible_facts.os_family`.

---

### Next Steps

* Replace password auth with SSH keys for production.
* Add more hosts by expanding the inventory.
* Use **Tags** (`--tags nginx`) and **Check mode** (`--check`) to demonstrate safe changes.
* Explore Ansible Vault further to store SSH keys and API tokens securely.

Happy automating 🚀