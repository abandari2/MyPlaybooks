SERVICENOW NOTES
==================

The snow_record module of Ansible Creates/Deletes/Updates a single record in ServiceNow.

Prerequisites:
--------------

1) Access to Ansible Core
2) Access to snow environment with web_service_admin role
3) python pysnow (pysnow) installed on ansible host

Playbooks:
-------------

Below examples interacts with the instance of ServiceNow to create/delete/update an incident

snow_create_incident.yml
-------------------------------
```
---
- hosts: localhost
   vars_prompt:
     - name: user
       prompt: "Enter your username"
     - name: password
       prompt: "Enter your password"
   tasks:
   - name: Create an incident
     snow_record:
       username: "{{ user }}"
       password: "{{ password }}"
       instance: name
       state: present
       data:
        short_description: "This is a test incident opened by Ansible"
        severity: 3
        priority: 2
     register: new_incident
  - debug: var=new_incident
```
snow_update_incident.yml
----------------------------------
```
---
- hosts: localhost
  #############
  # hosts is a required attribute
  connection: local
  gather_facts: no
  ignore_errors: True
  vars_prompt:
    - name: user
      prompt: "Enter your username"
    - name: password
      prompt: "Enter your password"
  tasks:
    - name: Update an incident
      snow_record:
        username: "{{ user }}"
        password: "{{ password }}"
        instance: name
        state: present
        number: INC0099999
        data:
          work_notes : "Been working all day on this thing."
```
snow_delete_incident.yml
------------------------
```
---
- hosts: localhost
  connection: local
  gather_facts: no
  ignore_errors: True
  vars_prompt:
    - name: user
      prompt: "Enter your username"
    - name: password
      prompt: "Enter your password"
  tasks:
    - name: Delete an incident
      snow_record:
        username: "{{ user }}"
        password: "{{ password }}"
        instance: name
        state: absent
        number: INC0099999
```
Steps to run the code:
--------------------------
ansible-playbook snow_XXXXX_incident.yml

For more information:
---------------------------
https://docs.ansible.com/ansible/2.5/modules/snow_record_module.html
