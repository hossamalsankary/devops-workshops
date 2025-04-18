# Ansible Role: `my_role`

This role demonstrates the standard Ansible role structure, how to create one, and how to use it.

## What is an Ansible Role?

An **Ansible role** is a reusable, self-contained set of Ansible tasks, handlers, variables, files, templates, and metadata. Roles simplify organizing your playbooks by grouping related content and make sharing and reusing automation across projects straightforward.

## Creating a New Role

Use the `ansible-galaxy` command to scaffold a new role:

```bash
ansible-galaxy role init my_role
```

This creates the following directory structure:

```
my_role/
├── defaults/
│   └── main.yml         # Weak default variables (lowest precedence)
├── vars/
│   └── main.yml         # Strong variables (higher precedence than defaults)
├── tasks/
│   └── main.yml         # Core task list
├── handlers/
│   └── main.yml         # Handlers for service reloads, etc.
├── files/
│   └── …                # Static files to deploy with copy or unarchive
├── templates/
│   └── nginx.conf.j2    # Jinja2 templates for dynamic configs
├── meta/
│   └── main.yml         # Role metadata and dependencies
```

## Simple Example

In `roles/my_role/tasks/main.yml`:

```yaml
- name: Print the welcome message
  debug:
    msg: "{{ welcome_message }}"
```

In `roles/my_role/defaults/main.yml`:

```yaml
welcome_message: "Hello, Ansible!"
```

Use the role in your playbook:

```yaml
- hosts: all
  roles:
    - my_role
```

## Running the Playbook

You can override variables on the command line with `-e` (extra_vars). For example:

```bash
ansible-playbook site.yml -i inventory.ini -e "welcome_message='Hi from CLI'"
```

This sets `welcome_message` to `Hi from CLI` for that run.
