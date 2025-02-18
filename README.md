# ansible-mongodb-atlas

Ansible modules for provisioning users and clusters with MongoDB Atlas.

Example:

~~Create a config file for your atlas credentials (~/.atlas.ini):~~

> Update: Please don't do that: you're committing credentials to a source code repository! Instead, this version expects the environment to be securely set with the following three variables. `{{ atlas_username }}` `{{ atlas_api_key }}`  and  `{{ atlas_group_id }}`

This repository has also been configured so that you can pull it down with `ansible-galaxy`, e.g.
`ansible-galaxy install --force --roles-path ./roles -r ./roles/requirements.yml` with a roles/requirements.yml that contains:

```yaml
---
- src: git+https://github.com/scotartt/ansible-mongodb-atlas.git

```

This will pull down the role into the directory `roles/ansible-mongodb-atlas` under your current directory.

tasks_sample/main.yml:
```
---

- name: create MongoDB Atlas clusters
  with_items: "{{ clusters }}"
  mongo_atlas_cluster:
    atlas_username: "{{ atlas_username }}"
    atlas_api_key: "{{ atlas_api_key }}"
    atlas_group_id: "{{ atlas_group_id }}"
    name: "{{ item.name }}"
    instance_size: "{{ item.size }}"
    encrypt: "{{ item.encrypt }}"
    backup_enabled: "{{ item.backup }}"
    state: "{{ item.state }}"
    region_name: "{{ item.region }}"

- name: create MongoDB Atlas users
  with_items: "{{ users }}"
  mongo_atlas_user:
    atlas_username: "{{ atlas_username }}"
    atlas_api_key: "{{ atlas_api_key }}"
    atlas_group_id: "{{ atlas_group_id }}"
    user: "{{ item.user }}"
    state: "{{ item.state }}"
    update_password: on_create
    roles: "{{ item.roles }}"
    password: "{{ item.password }}"
```


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
