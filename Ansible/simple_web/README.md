# simple_web Role

## What it does
1. Installs NGINX (using the OS’s default package manager)
2. Copies a `logo.png` from `files/`
3. Renders `index.html.j2` into the web root

## Variables
All variables live in `defaults/main.yml`:
- `nginx_pkg` (default: `nginx`)
- `web_root` (default: `/var/www/html`)
- `page_title` (default uses hostname)
- file ownership & permissions

## Usage
Include in any playbook:
```yaml
- hosts: webservers
  become: yes
  roles:
    - simple_web
```
