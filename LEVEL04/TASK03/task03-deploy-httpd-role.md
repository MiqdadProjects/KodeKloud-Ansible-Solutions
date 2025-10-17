# 🌟 **TASK03: Deploy httpd Role with Jinja2 Template**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* needs to deploy the Apache web server (`httpd`) on *App Server 1* in *Stratos DC* using an Ansible role. The role should install `httpd`, create a custom index page from a Jinja2 template, and ensure the service is running with the correct configuration.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use `/home/thor/ansible/inventory` on the jump host.  
2. **Ansible Role**: Create a role named `httpd_role` in `/home/thor/ansible/roles/httpd_role` to:  
   - Install the `httpd` package (default version from yum).  
   - Deploy `/home/thor/ansible/roles/httpd_role/templates/index.html.j2` to `/var/www/html/index.html` with `apache:apache` ownership and `0644` permissions.  
   - Start and enable the `httpd` service.  
3. **Playbook**: Create `/home/thor/ansible/playbook.yml` to apply the `httpd_role` to *App Server 1* (`stapp01`).  
4. **Execution**: Run with:  
   ```bash
   ansible-playbook -i inventory playbook.yml
   ```
5. **Template Content**: The `index.html.j2` should include a dynamic variable `server_name` set to the `inventory_hostname` of the target server.

👉 **Your Mission**: Create an Ansible role to deploy `httpd` on `stapp01`, use a Jinja2 template for a custom index page, and ensure the web server is running and accessible.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, inventory at `/home/thor/ansible/inventory`.  
  - *App Server*: `stapp01` (IP: `172.16.238.10`, User: `tony`, Password: `Ir0nM@n`).  
  - *Role Directory*: `/home/thor/ansible/roles/httpd_role`  
  - *Playbook File*: `/home/thor/ansible/playbook.yml`  
  - *Template File*: `/home/thor/ansible/roles/httpd_role/templates/index.html.j2`  
  - *Destination File*: `/var/www/html/index.html`  
  - *Service*: `httpd`  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Defines `stapp01` for Ansible management.  
- *Ansible Role*: `httpd_role` encapsulates tasks for installing `httpd`, deploying a template, and managing the service.  
- *Template*: `index.html.j2` generates a dynamic `index.html` with the server’s hostname.  
- *Playbook*: Applies `httpd_role` to `stapp01`.  
- *App Server 1*: Runs `httpd` with a custom index page accessible via HTTP.  

### **Implementation Strategy**  
1. Verify the inventory file for `stapp01` details.  
2. Create the `httpd_role` structure using `ansible-galaxy`.  
3. Define tasks in `httpd_role/tasks/main.yml` to install `httpd`, deploy the template, and start the service.  
4. Create `index.html.j2` in `httpd_role/templates/` with dynamic content.  
5. Create `/home/thor/ansible/playbook.yml` to apply the role.  
6. Execute the playbook and verify the setup (service status, file deployment, and web page content).  

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Role Structure Incorrect**:  
   - *Issue*: Missing or incorrect role directories/files.  
   - *Fix*: Use `ansible-galaxy init` to create the correct structure.  
2. **Package Installation Failure**:  
   - *Issue*: `httpd` package not found in yum.  
   - *Fix*: Verify yum repositories.  
     ```bash
     ansible stapp01 -i inventory -m command -a "yum repolist"
     ```  
3. **Template Not Found**:  
   - *Issue*: `index.html.j2` missing or incorrect path.  
   - *Fix*: Verify template path in `httpd_role/templates/`.  
4. **Service Failure**:  
   - *Issue*: `httpd` fails to start due to configuration errors or SELinux.  
   - *Fix*: Check logs and SELinux context.  
     ```bash
     ansible stapp01 -i inventory -m command -a "journalctl -u httpd"
     ansible stapp01 -i inventory -m command -a "sudo getenforce"
     ```  
5. **Permission Issues**:  
   - *Issue*: Incorrect ownership/permissions on `/var/www/html/index.html`.  
   - *Fix*: Ensure `template` task sets `apache:apache` and `0644`.  
6. **Variable Not Rendered**:  
   - *Issue*: `index.html` does not include `server_name`.  
   - *Fix*: Verify `index.html.j2` uses `{{ server_name }}` and `server_name` is passed in the playbook.  

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```
*Purpose*: Access the jump host as user `thor`.

### **Step 2: Navigate to Ansible Directory**  
```bash
cd /home/thor/ansible
```
*Purpose*: Move to the directory containing the inventory and playbook.

### **Step 3: Verify Inventory File**  
```bash
cat /home/thor/ansible/inventory
```
**Expected Content**:
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
*Purpose*: Confirm inventory details for `stapp01`.

### **Step 4: Create Ansible Role**  
```bash
ansible-galaxy init roles/httpd_role
```
*Purpose*: Initialize the `httpd_role` structure in `/home/thor/ansible/roles/httpd_role`.

### **Step 5: Create Role Tasks**  
```bash
vi roles/httpd_role/tasks/main.yml
```
**Content**:
```yaml
---
- name: Install httpd package
  yum:
    name: httpd
    state: present
- name: Deploy index.html from template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
    owner: apache
    group: apache
    mode: '0644'
- name: Start and enable httpd service
  service:
    name: httpd
    state: started
    enabled: yes
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Define tasks to install `httpd`, deploy the template, and manage the service.

### **Step 6: Create Jinja2 Template**  
```bash
vi roles/httpd_role/templates/index.html.j2
```
**Content**:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to {{ server_name }}</title>
</head>
<body>
    <h1>Hello from {{ server_name }}!</h1>
    <p>This is the Apache web server running on {{ server_name }}.</p>
</body>
</html>
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a dynamic `index.html` with the server’s hostname.

### **Step 7: Create Playbook**  
```bash
vi playbook.yml
```
**Content**:
```yaml
---
- name: Deploy httpd role on App Server 1
  hosts: stapp01
  become: yes
  vars:
    server_name: "{{ inventory_hostname }}"
  roles:
    - httpd_role
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Apply the `httpd_role` to `stapp01` with the `server_name` variable.

### **Step 8: Run Playbook**  
```bash
ansible-playbook -i inventory playbook.yml
```
**Expected Output**:
```bash
PLAY [Deploy httpd role on App Server 1] ***************************************

TASK [Gathering Facts] ********************************************************
ok: [stapp01]

TASK [httpd_role : Install httpd package] **************************************
changed: [stapp01]

TASK [httpd_role : Deploy index.html from template] ****************************
changed: [stapp01]

TASK [httpd_role : Start and enable httpd service] *****************************
changed: [stapp01]

PLAY RECAP ********************************************************************
stapp01                    : ok=4    changed=3    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to deploy the role.

### **Step 9: Verify Setup**  
```bash
ansible stapp01 -i inventory -m shell -a "sudo systemctl status httpd | grep Active"
ansible stapp01 -i inventory -m shell -a "ls -l /var/www/html/index.html"
ansible stapp01 -i inventory -m shell -a "curl http://localhost/index.html"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
     Active: active (running) since Fri 2025-10-17 19:05:54 PKT; <time> ago
stapp01 | CHANGED | rc=0 >>
-rw-r--r-- 1 apache apache 150 Oct 17 19:05 /var/www/html/index.html
stapp01 | CHANGED | rc=0 >>
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to stapp01</title>
</head>
<body>
    <h1>Hello from stapp01!</h1>
    <p>This is the Apache web server running on stapp01.</p>
</body>
</html>
```
*Purpose*: Confirm `httpd` service status, file permissions, and dynamic content rendering.

---

## 🔍 **Code Analysis**  
![Section](https://img.shields.io/badge/Section-Code%20Analysis-blue?style=flat-square)  

### **Inventory File** (`/home/thor/ansible/inventory`)  
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
- *Hosts*: `stapp01` used for this task.  
- *Details*: IP, username, and password for SSH access.  
*Purpose*: Defines target server.

### **Role Tasks** (`/home/thor/ansible/roles/httpd_role/tasks/main.yml`)  
```yaml
---
- name: Install httpd package
  yum:
    name: httpd
    state: present
- name: Deploy index.html from template
  template:
    src: index.html.j2
    dest: /var/www/html/index.html
    owner: apache
    group: apache
    mode: '0644'
- name: Start and enable httpd service
  service:
    name: httpd
    state: started
    enabled: yes
```
- *Tasks*: Installs `httpd`, deploys `index.html`, starts/enables service.  
*Purpose*: Encapsulates reusable logic for `httpd` deployment.

### **Jinja2 Template** (`/home/thor/ansible/roles/httpd_role/templates/index.html.j2`)  
```html
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to {{ server_name }}</title>
</head>
<body>
    <h1>Hello from {{ server_name }}!</h1>
    <p>This is the Apache web server running on {{ server_name }}.</p>
</body>
</html>
```
- *Variables*: Uses `{{ server_name }}` for dynamic content.  
*Purpose*: Generates a custom `index.html` with the server’s hostname.

### **Playbook** (`/home/thor/ansible/playbook.yml`)  
```yaml
---
- name: Deploy httpd role on App Server 1
  hosts: stapp01
  become: yes
  vars:
    server_name: "{{ inventory_hostname }}"
  roles:
    - httpd_role
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: Targets `stapp01`.  
- *Privilege Escalation*: `become: yes` for root access.  
- *Variables*: Sets `server_name` to `inventory_hostname`.  
- *Roles*: Applies `httpd_role`.  
*Purpose*: Orchestrates role execution.

### **Resource Attributes**  
| **Resource** | **Attribute** | **Value** | **Description** |
|--------------|---------------|-----------|-----------------|
| Inventory    | Hostname      | `stapp01` | App Server 1 alias |
| Inventory    | ansible_host  | `172.16.238.10` | App Server 1 IP |
| Inventory    | ansible_user  | `tony`    | App Server 1 user |
| Inventory    | ansible_ssh_pass | `Ir0nM@n` | App Server 1 password |
| Playbook     | Hosts         | `stapp01` | Targets App Server 1 |
| Playbook     | Variable      | `server_name` | Set to `inventory_hostname` |
| Role         | Package       | `httpd`   | Installed package |
| Role         | File Path     | `/var/www/html/index.html` | Deployed file |
| Role         | Ownership     | `apache:apache` | Owner and group |
| Role         | Permissions   | `0644`    | File permissions |
| Role         | Service       | `httpd`   | Web server service |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat /home/thor/ansible/inventory
```
*Purpose*: Confirm `stapp01` details.

### **Step 2: Verify Role Structure**  
```bash
ls -R /home/thor/ansible/roles/httpd_role
```
**Expected Output**:
```bash
/home/thor/ansible/roles/httpd_role:
tasks  templates

/home/thor/ansible/roles/httpd_role/tasks:
main.yml

/home/thor/ansible/roles/httpd_role/templates:
index.html.j2
```
*Purpose*: Ensure role directories and files exist.

### **Step 3: Verify Playbook Execution**  
```bash
ansible-playbook -i inventory playbook.yml
```
*Purpose*: Confirm successful playbook run.

### **Step 4: Verify Service Status**  
```bash
ansible stapp01 -i inventory -m shell -a "sudo systemctl status httpd | grep Active"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
     Active: active (running) since Fri 2025-10-17 19:05:54 PKT; <time> ago
```
*Purpose*: Verify `httpd` is running.

### **Step 5: Verify File Deployment**  
```bash
ansible stapp01 -i inventory -m shell -a "ls -l /var/www/html/index.html"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
-rw-r--r-- 1 apache apache 150 Oct 17 19:05 /var/www/html/index.html
```
*Purpose*: Confirm `index.html` exists with correct ownership and permissions.

### **Step 6: Verify Template Rendering**  
```bash
ansible stapp01 -i inventory -m shell -a "curl http://localhost/index.html"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to stapp01</title>
</head>
<body>
    <h1>Hello from stapp01!</h1>
    <p>This is the Apache web server running on stapp01.</p>
</body>
</html>
```
*Purpose*: Verify `index.html` contains dynamic `server_name` (`stapp01`).

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test SSH Connectivity**  
```bash
ssh tony@172.16.238.10
```
*Purpose*: Verify SSH access to `stapp01`.

### **Test Playbook Syntax**  
```bash
ansible-playbook -i inventory playbook.yml --syntax-check
```
**Expected Output**:
```bash
playbook: playbook.yml
```
*Purpose*: Validate YAML syntax.

### **Test Dry Run**  
```bash
ansible-playbook -i inventory playbook.yml --check
```
*Purpose*: Simulate playbook execution without changes.

### **Test Package Installation**  
```bash
ansible stapp01 -i inventory -m command -a "yum list installed httpd"
```
**Expected Output**:
```bash
stapp01 | CHANGED | rc=0 >>
Installed Packages
httpd.x86_64            <version>            @<repo>
```
*Purpose*: Confirm `httpd` is installed.

### **Test Template Existence**  
```bash
cat /home/thor/ansible/roles/httpd_role/templates/index.html.j2
```
**Expected Output**:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Welcome to {{ server_name }}</title>
</head>
<body>
    <h1>Hello from {{ server_name }}!</h1>
    <p>This is the Apache web server running on {{ server_name }}.</p>
</body>
</html>
```
*Purpose*: Verify template content.

### **Test Web Server Access**  
```bash
ansible stapp01 -i inventory -m shell -a "curl http://localhost"
```
*Purpose*: Confirm `httpd` serves the custom `index.html`.

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
# Navigate to directory
cd /home/thor/ansible
# Create role
ansible-galaxy init roles/httpd_role
# Create role tasks
vi roles/httpd_role/tasks/main.yml
# Create template
vi roles/httpd_role/templates/index.html.j2
# Create playbook
vi playbook.yml
# Run playbook
ansible-playbook -i inventory playbook.yml
# Verify
ansible stapp01 -i inventory -m shell -a "sudo systemctl status httpd | grep Active"
ansible stapp01 -i inventory -m shell -a "ls -l /var/www/html/index.html"
ansible stapp01 -i inventory -m shell -a "curl http://localhost/index.html"
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Role Not Found**  
*Symptoms*: Playbook fails with `role 'httpd_role' not found`.  
*Solution*: Verify role path.  
```bash
ls -R /home/thor/ansible/roles/httpd_role
```

### **Issue 2: Package Installation Fails**  
*Symptoms*: `yum` task fails with package not found.  
*Solution*: Check yum repositories.  
```bash
ansible stapp01 -i inventory -m command -a "yum repolist"
```

### **Issue 3: Template Not Deployed**  
*Symptoms*: `index.html` missing or contains raw Jinja2 code.  
*Solution*: Verify template path and variable rendering.  
```bash
cat /home/thor/ansible/roles/httpd_role/templates/index.html.j2
ansible stapp01 -i inventory -m shell -a "cat /var/www/html/index.html"
```

### **Issue 4: Service Not Running**  
*Symptoms*: `httpd` fails to start.  
*Solution*: Check logs and SELinux context.  
```bash
ansible stapp01 -i inventory -m shell -a "journalctl -u httpd"
ansible stapp01 -i inventory -m shell -a "sudo getenforce"
ansible stapp01 -i inventory -m shell -a "sudo chcon -R -t httpd_sys_content_t /var/www/html"
```

### **Issue 5: Incorrect Permissions**  
*Symptoms*: `index.html` has wrong owner or permissions.  
*Solution*: Verify `template` task settings.  
```bash
ansible stapp01 -i inventory -m shell -a "ls -l /var/www/html/index.html"
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Apply SELinux context for Apache in production.  
  ```bash
  ansible stapp01 -i inventory -m shell -a "sudo chcon -R -t httpd_sys_content_t /var/www/html"
  ```  
- *Idempotency*: Role tasks (`yum`, `template`, `service`) ensure safe re-runs.  
- *Verbose Output*: Use `-v` for debugging:  
  ```bash
  ansible-playbook -i inventory playbook.yml -v
  ```  
- *Role Reusability*: `httpd_role` can be reused for other servers by updating the playbook’s `hosts`.  
- *Backup*: Back up `/var/www/html` before deployment.  
  ```bash
  ansible stapp01 -i inventory -m copy -a "src=/var/www/html dest=/var/www/html.bak recurse=yes"
  ```  
- *Testing*: Test the web page in a browser if `stapp01` is externally accessible (e.g., `http://172.16.238.10`).  

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Role Creation*: Setting up the correct role structure and ensuring all tasks are included.  
2. *Template Rendering*: Ensuring `server_name` is dynamically populated in `index.html`.  
3. *Service Management*: Ensuring `httpd` starts and remains enabled.  

### **Solution Approach**  
1. Used `ansible-galaxy init` to create a standard role structure.  
2. Defined `server_name` as `inventory_hostname` in the playbook to ensure dynamic rendering.  
3. Used `service` module to start and enable `httpd` with idempotency.  

### **Key Success Factors**  
- *Role Structure*: Correctly placed tasks and templates in `httpd_role`.  
- *Variable Usage*: Passed `server_name` via playbook variables.  
- *Verification*: Confirmed service status, file deployment, and dynamic content.  

### **Critical Details**  
- *Inventory*: `/home/thor/ansible/inventory`  
- *Role*: `/home/thor/ansible/roles/httpd_role`  
- *Playbook*: `/home/thor/ansible/playbook.yml`  
- *Template*: `/home/thor/ansible/roles/httpd_role/templates/index.html.j2`  
- *File*: `/var/www/html/index.html`  
- *Service*: `httpd`  

### **Ansible Benefits**  
- *Modularity*: Roles encapsulate reusable logic.  
- *Dynamic Content*: Jinja2 templates enable customization.  
- *Automation*: Simplifies `httpd` deployment and configuration.  

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Configure `httpd` with SSL/TLS and disable directory indexing.  
  ```bash
  ansible stapp01 -i inventory -m lineinfile -a "path=/etc/httpd/conf/httpd.conf line='Options -Indexes' insertafter='^<Directory \"/var/www/html\">'"
  ```  
- 📊 *Logging*: Enable Apache and Ansible logging for auditing.  
  ```bash
  export ANSIBLE_LOG_PATH=/var/log/ansible.log
  ansible stapp01 -i inventory -m command -a "tail /var/log/httpd/access_log"
  ```  
- 🛡️ *Permissions*: Restrict `/var/www/html` access in production.  
- 🌐 *Scalability*: Extend the role to additional servers by updating the playbook’s `hosts`.  

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Ansible Roles*: Creating and using roles for modular automation.  
- *Jinja2 Templates*: Deploying dynamic content with variables.  
- *Service Management*: Starting and enabling services with Ansible.  

**Ansible Features Used**:  
- `ansible-galaxy` for role initialization.  
- `yum` for package installation.  
- `template` for dynamic file deployment.  
- `service` for managing `httpd`.  

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Verified `/home/thor/ansible/inventory` for `stapp01`.  
- *Role*: Created `httpd_role` with tasks and template.  
- *Playbook*: Created `/home/thor/ansible/playbook.yml` to apply the role.  
- *Execution*: Ran successfully.  
- *Verification*: Confirmed service status, file deployment, and dynamic content rendering.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: Apache web server is running on `stapp01` with a custom `index.html` displaying `stapp01` as the server name, deployed via an Ansible role.