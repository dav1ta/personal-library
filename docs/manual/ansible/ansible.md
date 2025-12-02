# Ansible Tutorial: Real-Life Examples and Features

## Advanced Features

### Dynamic Inventories

Instead of static hosts lists, Ansible can use dynamic inventories that pull host information from external sources, like cloud providers.

```shell
ansible-inventory -i inventory_aws_ec2.yml --graph
```

### Templating with Jinja2

Ansible utilizes Jinja2 templating to dynamically generate files or variables based on the inventory data.

```yaml
# template.j2
Hello, {{ user_name }}! Welcome to {{ service_name }}.
```

### Conditional Execution

Execute tasks based on conditions.

```yaml
- name: Restart nginx only on Debian systems
  ansible.builtin.service:
    name: nginx
    state: restarted
  when: ansible_facts['os_family'] == "Debian"
```

### Loops

Perform tasks on a list of items.

```yaml
- name: Install multiple packages
  ansible.builtin.yum:
    name: "{{ item }}"
    state: present
  loop:
    - nginx
    - nodejs
```

### Error Handling

Use blocks to handle errors and perform cleanup.

```yaml
- name: Attempt and clean up task
  block:
    - name: Attempt to do something
      ansible.builtin.command: /bin/false
  rescue:
    - name: Clean up after failure
      ansible.builtin.file:
        path: /some/path
        state: absent
```

## Real-Life Example Scenarios

### Configuration Management

Automatically configure and maintain consistency of settings and software on servers.

```yaml
- hosts: webservers
  roles:
    - role: nginx
    - role: php-fpm
    - role: letsencrypt
```

### Continuous Deployment

Deploy applications automatically to different environments after passing CI/CD pipelines.

```yaml
- hosts: production_servers
  tasks:
    - name: Pull latest code from Git
      ansible.builtin.git:
        repo: 'https://example.com/repo.git'
        dest: /var/www/html/app
        version: master
    - name: Restart application service
      ansible.builtin.service:
        name: my_app
        state: restarted
```

### Infrastructure Provisioning

Provision and manage infrastructure on cloud platforms.

```yaml
- hosts: localhost
  tasks:
    - name: Create AWS EC2 instances
      community.aws.ec2_instance:
        name: "web-server"
        state: present
        image_id: ami-123456
        instance_type: t2.micro
```

### Security Automation

Automatically enforce security policies and configurations.

```yaml
- hosts: all
  tasks:
    - name: Ensure password authentication is disabled in sshd config
      ansible.builtin.lineinfile:
        path: /etc/ssh/sshd_config
        regexp: '^#?PasswordAuthentication'
        line: 'PasswordAuthentication no'
```

### Network Automation

Configure and manage network devices across data centers.

```yaml
- hosts: switches
  tasks:
    - name: Set VLAN configuration
      community.network.ios_vlan:
        vlan_id: 100
        name: "User_VLAN"
        state: present
```

These examples and features showcase the versatility and power of Ansible in real-world scenarios, from simple configuration management to sophisticated automation workflows across IT infrastructure.

