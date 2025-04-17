
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
