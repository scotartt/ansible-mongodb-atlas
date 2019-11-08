# ansible-mongodb-atlas

Ansible modules for provisioning users and clusters with MongoDB Atlas.

Example:

-Create a config file for your atlas credentials (~/.atlas.ini):-

> Update: Please don't do that: you're committing credentials to a source code repository! Instead, this version expects the environment to be securely set with the following three variables. `{{ atlas_username }}` `{{ atlas_api_key }}`  and  `{{ atlas_group_id }}`

This repository has also been configured so that you can pull it down with `ansible-galaxy`, e.g.
`ansible-galaxy install --force --roles-path ./roles -r ./roles/requirements.yml` with a roles/requirements.yml that contains:

```yaml
---
- src: git+https://github.com/scotartt/ansible-mongodb-atlas.git

```

This will pull down the role into the directory `roles/ansible-mongodb-atlas` under your current directory.

The tasks file `roles/ansible-mongodb-atlas/tasks/main.yml` is now supplied in the package, you don't have to create it.

To use it, just specify the details in your playbook, an example is below. The Atlas API username, API key, and your 'group id' for Atlas need to be securely sourced (not in your code repo!), as well as the mongo DB usernames and *especially* passwords.

mongodb_atlas.yml:

```yaml
---
- hosts: localhost
  connection: local
  roles:
    - role: ansible-mongodb-atlas
      clusters:
        - name: test-mongo
          size: M10
          state: present
        - name: staging-mongo
          size: M30
          state: present
        - name: prod-mongo
          size: M30
          state: present
      users:
        - user: mongo_app_user
          password: "{{ lookup('password', 'creds/atlas/mongo_app_user chars=ascii_letters') }}"
          state: present
          roles:
            - readWrite
```

Simply run `ansible-playbook mongodb_atlas.yml`
