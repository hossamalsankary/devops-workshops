
---

## 2 · Deploy a Simple Web Page

`web.yml`

```yaml
---
- name: Install Docker on Ubuntu
  hosts: all
  become: true
  tasks:
    - name: Add Docker GPG apt Key
      apt_key:
        url: https://download.docker.com/linux/ubuntu/gpg
        state: present

    - name: Add Docker Repository
      apt_repository:
        repo: deb https://download.docker.com/linux/ubuntu {{ ansible_distribution_release }} stable
        state: present

    - name: Update apt and install docker-ce
      apt:
        name: docker-ce
        state: latest
        update_cache: true

    - name: Install Docker 
      apt:
        name: docker

```

Run & test:

```bash
ansible-playbook -i inventory.ini web.yml
curl http://192.168.56.20
```

---
