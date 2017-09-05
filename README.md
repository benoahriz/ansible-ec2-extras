# ansible-ec2-extras


This is a module based on the ec2_remote_facts module that adds a sigle field that isnt available in the default.  [instance_type](http://docs.aws.amazon.com/AWSEC2/latest/APIReference/API_DescribeHosts.html)


The following is a simple example of using the ec2_remote_facts_extra to create a csv report of the instance names and types.  You can use this to add hosts to a group and filter on different attributes.


``` yaml

---

- name: Write a report about hosts
  gather_facts: false
  hosts: localhost
  tasks:

    - name: remote facts
      ec2_remote_facts_extra:
        filters:
          instance-state-name: running
      register: ec2_facts_other

    - file: path=/tmp/hosts_report state=absent

    - name: make a file with the infomation
      lineinfile:
        dest: /tmp/hosts_report
        line: "{{ item.0 }},{{ item.1 }},{{ item.2 }}"
        state: present
        insertafter: EOF
        create: True
      with_together:
        - "{{ ec2_facts_other.instances|map(attribute='tags.Name')|list }}"
        - "{{ ec2_facts_other.instances|map(attribute='instance_type')|list }}"
        - "{{ ec2_facts_other.instances|map(attribute='id')|list }}"

```

