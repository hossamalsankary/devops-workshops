## Understanding Ansible Roles Structure

Before we start creating our own role, let's understand the structure of an Ansible Role.

An Ansible Role has a defined directory structure with eight main standard directories. Each directory must contain a `main.yml` file, which contains the relevant content. Here's a brief overview of each directory:

1. `tasks` - contains the main list of tasks to be executed by the role.
2. `handlers` - contains handlers, which may be used by this role or outside of this role.
3. `defaults` - default variables for the role.
4. `vars` - other variables for the role.
5. `files` - contains files which can be deployed via this role.
6. `templates` - contains templates which can be deployed via this role.
7. `meta` - defines some meta data for this role.
8. `tests` - contains tests for the role.

Let's start by creating a simple role structure. First, navigate to your project directory:

```bash
cd ~/project
```

Now, let's create a directory for our roles:

```bash
mkdir -p roles/example_role
cd roles/example_role
```

The `-p` flag in the `mkdir` command creates parent directories as needed.

Now, let's create the basic structure for our role:

```bash
mkdir {tasks,handlers,defaults,vars,files,templates,meta}
```

This command creates all the directories we need for our role in one go. It's a handy shortcut in bash to create multiple directories at once.

Now, let's create a `main.yml` file in the `tasks` directory:

```bash
nano tasks/main.yml
```

Add the following content to this file:

```yml
---
- name: Print a message
  debug:
    msg: "This is a task from our example role!"
```

Save and exit the nano editor (Ctrl+X, then Y, then Enter).

We've now created a basic role structure with a simple task. In the next steps, we'll expand on this and learn how to use our role.

## Expanding Our Role

Now that we have a basic role structure, let's expand it to include more components of a typical Ansible role. We'll add variables, a handler, and a template.

First, let's add a default variable. Create a `main.yml` file in the `defaults` directory:

```bash
nano defaults/main.yml
```

Add the following content:

```yml
---
example_variable: "This is a default value"
```

This sets a default value for `example_variable`, which can be overridden when the role is used.

Next, let's create a handler. Create a `main.yml` file in the `handlers` directory:

```bash
nano handlers/main.yml
```

Add the following content:

```yml
---
- name: Restart example service
  debug:
    msg: "This would restart a service in a real scenario"
```

In a real-world scenario, this handler might restart a service, but for this example, we're just printing a message.

Now, let's create a template. Create a file named `example_template.j2` in the `templates` directory:

```bash
nano templates/example_template.j2
```

Add the following content:

```
This is an example template.
The value of example_variable is: {{ example_variable }}
```

This template uses Jinja2 syntax to include the value of our `example_variable`.

Finally, let's update our `tasks/main.yml` to use these new components:

```bash
nano tasks/main.yml
```

Replace the existing content with:

```yml
---
- name: Print a message
  debug:
    msg: "This is a task from our example role!"

- name: Use our variable
  debug:
    msg: "The value of example_variable is: {{ example_variable }}"

- name: Create a file from our template
  template:
    src: example_template.j2
    dest: /tmp/example_file.txt
  notify: Restart example service
```

This updated task list now uses our variable, creates a file from our template, and notifies our handler.

## Using Our Role in a Playbook

Now that we have created our role, let's use it in a playbook. First, navigate back to the project root:

```bash
cd ~/project
```

Create a new playbook file named `use_role.yml`:

```bash
nano use_role.yml
```

Add the following content:

```yml
---
- name: Use our example role
  hosts: localhost
  roles:
    - example_role
```

This playbook simply applies our `example_role` to the localhost.

Now, let's run this playbook:

```bash
ansible-playbook -i inventory.ini use_role.yml
```

You should see output showing the execution of the tasks we defined in our role.

Let's break down what happened:

1. Ansible looked for a role named `example_role` in the `roles` directory.
2. It found our role and executed the tasks defined in `tasks/main.yml`.
3. It used the default value for `example_variable` that we set in `defaults/main.yml`.
4. It created a file in `/tmp/example_file.txt` using our template.
5. Finally, it called our handler, which printed a debug message.

This demonstrates how roles allow us to package related tasks, variables, and files together, making our Ansible code more organized and reusable.

## Overriding Role Variables

One of the powerful features of Ansible roles is the ability to override default variables. This allows you to create flexible roles that can be used in different scenarios.

Let's create a new playbook that overrides our `example_variable`. Create a file named `override_role_var.yml`:

```bash
nano override_role_var.yml
```

Add the following content:

```yml
---
- name: Use our example role with a custom variable
  hosts: localhost
  vars:
    example_variable: "This is a custom value"
  roles:
    - example_role
```

In this playbook, we're setting `example_variable` to a custom value before applying our role.

Now, let's run this playbook:

```bash
ansible-playbook -i inventory.ini override_role_var.yml
```

You should see that the tasks now use the custom value for `example_variable` instead of the default value.

This demonstrates how you can create roles with sensible defaults, but still have the flexibility to customize their behavior when needed.

## Role Dependencies

Ansible roles can depend on other roles. This allows you to build more complex roles by combining simpler ones. Let's create a new role that depends on our `example_role`.

First, create a new role structure:

```bash
cd ~/project/roles
mkdir -p dependent_role/{tasks,meta}
```

Now, let's define the role dependencies. Create a `main.yml` file in the `meta` directory:

```bash
nano dependent_role/meta/main.yml
```

Add the following content:

```yml
---
dependencies:
  - role: example_role
```

This specifies that `dependent_role` depends on `example_role`.

Now, let's add a task to our `dependent_role`. Create a `main.yml` file in the `tasks` directory:

```bash
nano dependent_role/tasks/main.yml
```

Add the following content:

```yml
---
- name: Task from dependent role
  debug:
    msg: "This task is from the dependent role"
```

Now, let's create a playbook to use our `dependent_role`. Navigate back to the project root:

```bash
cd ~/project
nano use_dependent_role.yml
```

Add the following content:

```yml
---
- name: Use our dependent role
  hosts: localhost
  roles:
    - dependent_role
```

Finally, let's run this playbook:

```bash
ansible-playbook -i inventory.ini use_dependent_role.yml
```

