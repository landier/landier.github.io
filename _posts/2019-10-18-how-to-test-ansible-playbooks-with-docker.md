---
layout: post
title: How to test your Ansible playbook using Docker
date:   2017-10-18 00:00:00 +0200
categories: [linux,docker,ansible]
---

# 1. Create a test wrapping Ansible playbook **test.yml#**

```
- name: Test playbook
  hosts: all
  gather_facts: no
  tags:
    - test-wrapper
    - start-containers
  tasks:
    - name: "Start test container"
      delegate_to: localhost
      docker_container:
        name: "{{ inventory_hostname }}"
        image: "{{ docker_image }}"
        command: sleep 30m
        state: started
        recreate: yes
        cleanup: yes
        detach: true
        interactive: true
        tty: true

    - name: Install python and sudo inside the container
      raw: apt -y update && apt install -y python3-minimal python3-urllib3 sudo

    - name: Create sudo group
      when: user is defined
      group:
        name: sudo
        system: yes

    - name: Create user
      when: user is defined
      user:
        name: "{{ user }}"
        uid: 1000
        groups: adm,sudo
        append: yes

    - name: No password prompt for sudo group
      lineinfile:
        dest: /etc/sudoers
        regexp: '^%sudo.*'
        line: '%sudo ALL=(ALL:ALL) NOPASSWD:ALL'
        state: present
        validate: 'visudo -cf %s'

- name: "Import {{ playbook }} playbook"
  import_playbook: "{{ playbook }}"

- name: "Idempotency check of {{ playbook }} playbook"
  import_playbook: "{{ playbook }}"

- hosts: all
  tags:
    - test-wrapper
    - stop-containers
  tasks:
    - name: "Stop {{ inventory_hostname }} container"
      delegate_to: localhost
      docker_container:
        name: "{{ inventory_hostname }}"
        state: absent
```

Notice the three tasks (Create sudo group, Create user and No password prompt for sudo group) which aims at reproducing a typical Ubuntu based system.

You can see mine [here](https://github.com/landier/playbooks/blob/master/test.yml).

# 2. Create a test iventory (**inventories/test.ini** in my case) that contains hosts matching your real inventory

```
[all:vars]
ansible_connection=docker
docker_image=ubuntu:disco

[my-first-group]
my-host-01 user=nlandier

[my-second-group]
another-host
```

# 3. Execute your test playbook

```
ansible-playbook -i inventories/test.ini test.yml -e playbook=my-lan-playbook.yml
```


# 4. *Optional* For conveniency, you may add a Makefile (or any build file) to make things easy both in dev and Continuous Integration environment which I typically execute on Travis CI
```
PLAYBOOK = my-lan-playbook.yml
TAGS = all
LIMIT = all
SKIP = none
OPTS = -vvvv

.DEFAULT_GOAL := all
.PHONY : all install lint test

all: lint test

install:
	python3 -m pip install ansible-lint docker-py

lint:
	-ansible-lint $(PLAYBOOK)

test:
	ansible-playbook -i inventories/test.ini test.yml -e playbook=$(PLAYBOOK) --limit $(LIMIT) --tags test-wrapper,$(TAGS) --skip-tags ignore,$(SKIP) $(OPTS)

```
