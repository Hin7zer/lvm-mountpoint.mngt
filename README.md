# Ansible role "disks+lvm_mountpoints" documentation

Date: 2024-02-15

Authors: Benjamin Hinz

## Requirements

Some variables which are used are not defined in this role itself. This could affect users for api calls for example.

## Description

With this role it is possible to manage in simple way the LVM on remote maschines. The use cases will described later. If this role will be extended the use cases can be updated easy.

### Tasks Overview

- Several checks on remote maschine for lvm like "does vg/lv/mountpoint/unused disk already exist?".
- Register latest unused disk device
- Attach disk via defined include options if not skipped
- Create new mountpoint (including new disk, vg and lv)
- Extend existing mountpoint

### Conditions

1. If skip_lvm is defined the tasks to etend or create a lvm can be skipped. Maybe some vars needs to be defined, depending on the integration in playbooks.
2. If tag is set to one of the tasks (create/extend) it will run neccessary checks and tasks to create a mountpoint or extend it.
3. If mountpoint exist it will skip the creation.
4. If unused disks are found it will fail (therefore you need to make sure no unused disks are attached before to the remote host).


## Use-Cases

### Use-Case 1: Create LVM mointpoint with lvm and attach disk

This use case will create a new mountpoint on the remote system. Make sure no unused disks are attached. if vg exists the vg will be extended for the lv/mountpoint.

**Make sure the user which should own the mountpoint exists!**

### Variables

The variables can be always be overwritten by extra vars on execution!

| Variable | Example |
|----------|---------|
|vg        |"vg_hello"|
|lv        |"bye"|
|size      |  "100%VG"|
|disksize  |  22|
|filesystem|"xfs"|
|mountpoint|"/var/lib/hello"|
|owner     | "root"|
|group     | "root" |
|mode      |  "0700"|
|lvm_path  |  "/dev/vg_name/lv_name"|

### Use-Case 2: Extend LVM mointpoint with lvm and attach disk

This use case will create a new mountpoint on the remote system. Make sure no unused disks are attached. if vg exists the vg will be extended for the lv/mountpoint.

**Make sure the user which should own the mountpoint exists!**

### Variables

The variables can be always be overwritten by extra vars on execution!

| Variable | Example |
|----------|---------|
|vg        |"vg_hello"|
|lv        |"bye"|
|disksize  |  22|

## Include this role

requirements.yml example:
```yaml
roles:
  - src: git@gitlab.url:path/to/role.git
    scm: git
    version: "1.0"
    name: example_role
```

```bash
ansible-galaxy install -r requirements.yml -p roles/
```

## Playbook example

Playbook content:
```yaml
- hosts: "{{ host }}"
  remote_user: custom_user
  become: yes
  vars:
    lvm_path: "/dev/{{vg}}/{{lv}}" 
  roles:
    - lvm-mountpoint.mngt
```

Execution example:
```bash
ansible-playbook -i host1, lvm_mointpoint.yml -t create -e host=all -e -t create -e vg="vg_test" -e lv="hello" -e size="100%VG" -e disksize=100 -e filesystem="ext4" -e mountpoint="/var/lib/hello" -e owner="root" -e group="root" -e mode='0755'
```