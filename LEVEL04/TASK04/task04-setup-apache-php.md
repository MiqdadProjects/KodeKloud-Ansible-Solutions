# 🌟 **TASK04: Setup Apache and PHP on App Server 2**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* needs to configure Apache and PHP on *App Server 2* in *Stratos DC* with a custom document root and a PHP info page to verify the setup.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use `/home/thor/playbooks/inventory` on the jump host.  
2. **Playbook**: Create `/home/thor/playbooks/httpd.yml` to:  
   - Install `httpd` and `php` packages (default versions from yum).  
   - Change the document root to `/var/www/html/myroot` in `/etc/httpd/conf/httpd.conf`.  
   - Ensure the directory `/var/www/html/myroot` exists.  
   - Deploy `/home/thor/playbooks/templates/phpinfo.php.j2` to `/var/www/html/myroot/phpinfo.php` with `apache:apache` ownership and `0644` permissions.  
   - Start and enable the `httpd` service.  
3. **Execution**: Run with:  
   ```bash
   ansible-playbook -i inventory httpd.yml
   ```

👉 **Your Mission**: Install Apache and PHP, configure a custom document root, deploy a PHP info page, and ensure the web server is running on `stapp02`.

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, Ansible installed, inventory at `/home/thor/playbooks/inventory`.  
  - *App Server*: `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`).  
  - *Playbook File*: `/home/thor/playbooks/httpd.yml`  
  - *Template File*: `/home/thor/playbooks/templates/phpinfo.php.j2`  
  - *Configuration File*: `/etc/httpd/conf/httpd.conf`  
  - *Destination File*: `/var/www/html/myroot/phpinfo.php`  
  - *Service*: `httpd`  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Defines `stapp02` for Ansible management.  
- *Playbook*: Uses `yum`, `file`, `replace`, `template`, and `service` modules to install packages, configure Apache, deploy the PHP file, and manage the service.  
- *Template*: `phpinfo.php.j2` creates a PHP info page for verification.  
- *App Server 2*: Runs `httpd` with PHP support and a custom document root.  

### **Implementation Strategy**  
1. Verify the inventory file for `stapp02` details.  
2. Create the Jinja2 template `phpinfo.php.j2` for the PHP info page.  
3. Create `/home/thor/playbooks/httpd.yml` to install packages, update the document root, create the directory, deploy the template, and start the service.  
4. Execute the playbook and verify the setup (file presence, permissions, service status, and PHP functionality).  

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Package Installation Failure**:  
   - *Issue*: `httpd` or `php` packages not found in yum.  
   - *Fix*: Verify yum repositories or network connectivity.  
     ```bash
     ansible stapp02 -i inventory -m command -a "yum repolist"
     ```  
2. **Document Root Misconfiguration**:  
   - *Issue*: `httpd.conf` not updated correctly, causing `httpd` to use the default document root.  
   - *Fix*: Ensure `regexp` in `replace` module matches the exact `DocumentRoot` and `<Directory>` lines.  
3. **Directory Missing**:  
   - *Issue*: `/var/www/html/myroot` does not exist, causing file deployment failure.  
   - *Fix*: Use `file` module to create the directory before deploying the template.  
4. **Template Not Found**:  
   - *Issue*: `/home/thor/playbooks/templates/phpinfo.php.j2` missing or incorrect path.  
   - *Fix*: Verify template file path and existence.  
5. **Service Failure**:  
   - *Issue*: `httpd` service fails to start due to configuration errors or SELinux.  
   - *Fix*: Check `httpd` configuration and logs.  
     ```bash
     ansible stapp02 -i inventory -m command -a "journalctl -u httpd"
     ```  
6. **PHP Not Working**:  
   - *Issue*: `phpinfo.php` does not render PHP info.  
   - *Fix*: Ensure `php` module is loaded in `httpd.conf` and restart the service.  

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```
*Purpose*: Access the jump host as user `thor`.

### **Step 2: Navigate to Playbooks Directory**  
```bash
cd /home/thor/playbooks
```
*Purpose*: Move to the directory containing the inventory and playbook.

### **Step 3: Verify Inventory File**  
```bash
cat /home/thor/playbooks/inventory
```
**Expected Content**:
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
*Purpose*: Confirm inventory details for `stapp02`.

### **Step 4: Create Jinja2 Template**  
```bash
mkdir -p templates
vi templates/phpinfo.php.j2
```
**Content**:
```php
<?php
phpinfo();
?>
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a template for the PHP info page.

### **Step 5: Create Playbook**  
```bash
vi httpd.yml
```
**Content**:
```yaml
---
- name: Setup Apache and PHP on App Server 2
  hosts: stapp02
  become: yes
  tasks:
    - name: Install Apache and PHP
      yum:
        name:
          - httpd
          - php
        state: present
    - name: Ensure new document root directory exists
      file:
        path: /var/www/html/myroot
        state: directory
        owner: apache
        group: apache
        mode: '0755'
    - name: Update DocumentRoot in Apache configuration
      replace:
        path: /etc/httpd/conf/httpd.conf
        regexp: '^DocumentRoot\s+"/var/www/html"'
        replace: 'DocumentRoot "/var/www/html/myroot"'
    - name: Update <Directory> section for new DocumentRoot
      replace:
        path: /etc/httpd/conf/httpd.conf
        regexp: '<Directory\s+"/var/www/html">'
        replace: '<Directory "/var/www/html/myroot">'
    - name: Deploy phpinfo.php from template
      template:
        src: templates/phpinfo.php.j2
        dest: /var/www/html/myroot/phpinfo.php
        owner: apache
        group: apache
        mode: '0644'
    - name: Enable and start httpd service
      service:
        name: httpd
        state: started
        enabled: yes
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to install `httpd` and `php`, configure the document root, deploy the PHP file, and start the service.

### **Step 6: Run Playbook**  
```bash
ansible-playbook -i inventory httpd.yml
```
**Expected Output**:
```bash
PLAY [Setup Apache and PHP on App Server 2] ************************************

TASK [Gathering Facts] ********************************************************
ok: [stapp02]

TASK [Install Apache and PHP] **************************************************
changed: [stapp02]

TASK [Ensure new document root directory exists] *******************************
changed: [stapp02]

TASK [Update DocumentRoot in Apache configuration] *****************************
changed: [stapp02]

TASK [Update <Directory> section for new DocumentRoot] *************************
changed: [stapp02]

TASK [Deploy phpinfo.php from template] ****************************************
changed: [stapp02]

TASK [Enable and start httpd service] ******************************************
changed: [stapp02]

PLAY RECAP ********************************************************************
stapp02                    : ok=7    changed=6    unreachable=0    failed=0
```
*Purpose*: Execute the playbook to apply the configuration.

### **Step 7: Verify Setup**  
```bash
ansible stapp02 -i inventory -m shell -a "sudo systemctl status httpd | grep Active"
ansible stapp02 -i inventory -m shell -a "ls -l /var/www/html/myroot/phpinfo.php"
ansible stapp02 -i inventory -m shell -a "grep DocumentRoot /etc/httpd/conf/httpd.conf"
ansible stapp02 -i inventory -m shell -a "curl http://localhost/myroot/phpinfo.php"
```
**Expected Output**:
```bash
stapp02 | CHANGED | rc=0 >>
     Active: active (running) since Fri 2025-10-17 17:15:54 PKT; <time> ago
stapp02 | CHANGED | rc=0 >>
-rw-r--r-- 1 apache apache 19 Oct 17 17:15 /var/www/html/myroot/phpinfo.php
stapp02 | CHANGED | rc=0 >>
DocumentRoot "/var/www/html/myroot"
stapp02 | CHANGED | rc=0 >>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "DTD/xhtml1-transitional.dtd">
<html><head>
<style type="text/css"> ... </style>
</head>
<body><h1>PHP Version ... </h1> ... </body></html>
```
*Purpose*: Confirm `httpd` service status, file permissions, document root configuration, and PHP functionality.

---

## 🔍 **Code Analysis**  
![Section](https://img.shields.io/badge/Section-Code%20Analysis-blue?style=flat-square)  

### **Inventory File** (`/home/thor/playbooks/inventory`)  
```ini
stapp01 ansible_host=172.16.238.10 ansible_user=tony ansible_ssh_pass=Ir0nM@n
stapp02 ansible_host=172.16.238.11 ansible_user=steve ansible_ssh_pass=Am3ric@
stapp03 ansible_host=172.16.238.12 ansible_user=banner ansible_ssh_pass=BigGr33n
```
- *Hosts*: `stapp02` used for this task.  
- *Details*: IP, username, and password for SSH access.  
*Purpose*: Defines target server.

### **Jinja2 Template** (`/home/thor/playbooks/templates/phpinfo.php.j2`)  
```php
<?php
phpinfo();
?>
```
- *Purpose*: Generates a PHP info page to verify PHP installation and configuration.

### **Playbook** (`/home/thor/playbooks/httpd.yml`)  
```yaml
---
- name: Setup Apache and PHP on App Server 2
  hosts: stapp02
  become: yes
  tasks:
    - name: Install Apache and PHP
      yum:
        name:
          - httpd
          - php
        state: present
    - name: Ensure new document root directory exists
      file:
        path: /var/www/html/myroot
        state: directory
        owner: apache
        group: apache
        mode: '0755'
    - name: Update DocumentRoot in Apache configuration
      replace:
        path: /etc/httpd/conf/httpd.conf
        regexp: '^DocumentRoot\s+"/var/www/html"'
        replace: 'DocumentRoot "/var/www/html/myroot"'
    - name: Update <Directory> section for new DocumentRoot
      replace:
        path: /etc/httpd/conf/httpd.conf
        regexp: '<Directory\s+"/var/www/html">'
        replace: '<Directory "/var/www/html/myroot">'
    - name: Deploy phpinfo.php from template
      template:
        src: templates/phpinfo.php.j2
        dest: /var/www/html/myroot/phpinfo.php
        owner: apache
        group: apache
        mode: '0644'
    - name: Enable and start httpd service
      service:
        name: httpd
        state: started
        enabled: yes
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: Targets `stapp02`.  
- *Privilege Escalation*: `become: yes` for root access.  
- *Tasks*:  
  - *Install*: Uses `yum` to install `httpd` and `php`.  
  - *Directory*: Creates `/var/www/html/myroot` with `file` module.  
  - *Config*: Updates `httpd.conf` with `replace` module for document root.  
  - *Template*: Deploys `phpinfo.php` using `template` module.  
  - *Service*: Starts and enables `httpd`.  
*Purpose*: Automates Apache and PHP setup with a custom document root.

### **Resource Attributes**  
| **Resource** | **Attribute** | **Value** | **Description** |
|--------------|---------------|-----------|-----------------|
| Inventory    | Hostname      | `stapp02` | App Server 2 alias |
| Inventory    | ansible_host  | `172.16.238.11` | App Server 2 IP |
| Inventory    | ansible_user  | `steve`   | App Server 2 user |
| Inventory    | ansible_ssh_pass | `Am3ric@` | App Server 2 password |
| Playbook     | Hosts         | `stapp02` | Targets App Server 2 |
| Playbook     | Packages      | `httpd`, `php` | Installed packages |
| Playbook     | Config File   | `/etc/httpd/conf/httpd.conf` | Apache configuration file |
| Playbook     | Document Root | `/var/www/html/myroot` | Custom document root |
| Playbook     | File Path     | `/var/www/html/myroot/phpinfo.php` | PHP info file |
| Playbook     | Ownership     | `apache:apache` | Owner and group for files |
| Playbook     | Permissions   | `0644`    | Permissions for `phpinfo.php` |
| Playbook     | Service       | `httpd`   | Web server service |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat /home/thor/playbooks/inventory
```
*Purpose*: Confirm `stapp02` details.

### **Step 2: Verify Template Existence**  
```bash
cat /home/thor/playbooks/templates/phpinfo.php.j2
```
**Expected Output**:
```php
<?php
phpinfo();
?>
```
*Purpose*: Ensure the template file exists and contains the correct content.

### **Step 3: Verify Playbook Execution**  
```bash
ansible-playbook -i inventory httpd.yml
```
*Purpose*: Confirm successful playbook run.

### **Step 4: Verify Service Status**  
```bash
ansible stapp02 -i inventory -m shell -a "sudo systemctl status httpd | grep Active"
```
**Expected Output**:
```bash
stapp02 | CHANGED | rc=0 >>
     Active: active (running) since Fri 2025-10-17 17:15:54 PKT; <time> ago
```
*Purpose*: Verify `httpd` is running.

### **Step 5: Verify Document Root Configuration**  
```bash
ansible stapp02 -i inventory -m shell -a "grep DocumentRoot /etc/httpd/conf/httpd.conf"
```
**Expected Output**:
```bash
stapp02 | CHANGED | rc=0 >>
DocumentRoot "/var/www/html/myroot"
```
*Purpose*: Confirm `DocumentRoot` is updated.

### **Step 6: Verify File Deployment**  
```bash
ansible stapp02 -i inventory -m shell -a "ls -l /var/www/html/myroot/phpinfo.php"
```
**Expected Output**:
```bash
stapp02 | CHANGED | rc=0 >>
-rw-r--r-- 1 apache apache 19 Oct 17 17:15 /var/www/html/myroot/phpinfo.php
```
*Purpose*: Confirm `phpinfo.php` exists with correct ownership and permissions.

### **Step 7: Verify PHP Functionality**  
```bash
ansible stapp02 -i inventory -m shell -a "curl http://localhost/myroot/phpinfo.php"
```
**Expected Output**:
```bash
stapp02 | CHANGED | rc=0 >>
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "DTD/xhtml1-transitional.dtd">
<html><head>
<style type="text/css"> ... </style>
</head>
<body><h1>PHP Version ... </h1> ... </body></html>
```
*Purpose*: Verify that `phpinfo.php` renders the PHP info page, confirming PHP is working.

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test SSH Connectivity**  
```bash
ssh steve@172.16.238.11
```
*Purpose*: Verify SSH access to `stapp02`.

### **Test Playbook Syntax**  
```bash
ansible-playbook -i inventory httpd.yml --syntax-check
```
**Expected Output**:
```bash
playbook: httpd.yml
```
*Purpose*: Validate YAML syntax.

### **Test Dry Run**  
```bash
ansible-playbook -i inventory httpd.yml --check
```
*Purpose*: Simulate playbook execution without changes.

### **Test Package Installation**  
```bash
ansible stapp02 -i inventory -m command -a "yum list installed httpd php"
```
**Expected Output**:
```bash
stapp02 | CHANGED | rc=0 >>
Installed Packages
httpd.x86_64            <version>            @<repo>
php.x86_64              <version>            @<repo>
```
*Purpose*: Confirm `httpd` and `php` are installed.

### **Test PHP Module**  
```bash
ansible stapp02 -i inventory -m shell -a "httpd -M | grep php"
```
**Expected Output**:
```bash
stapp02 | CHANGED | rc=0 >>
php_module (shared)
```
*Purpose*: Verify the PHP module is loaded in Apache.

### **Test Directory Existence**  
```bash
ansible stapp02 -i inventory -m shell -a "ls -ld /var/www/html/myroot"
```
**Expected Output**:
```bash
stapp02 | CHANGED | rc=0 >>
drwxr-xr-x 2 apache apache 4096 Oct 17 17:15 /var/www/html/myroot
```
*Purpose*: Confirm the custom document root directory exists.

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
# Navigate to directory
cd /home/thor/playbooks
# Create template
mkdir -p templates
vi templates/phpinfo.php.j2
# Create playbook
vi httpd.yml
# Run playbook
ansible-playbook -i inventory httpd.yml
# Verify
ansible stapp02 -i inventory -m shell -a "sudo systemctl status httpd | grep Active"
ansible stapp02 -i inventory -m shell -a "ls -l /var/www/html/myroot/phpinfo.php"
ansible stapp02 -i inventory -m shell -a "grep DocumentRoot /etc/httpd/conf/httpd.conf"
ansible stapp02 -i inventory -m shell -a "curl http://localhost/myroot/phpinfo.php"
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Package Installation Fails**  
*Symptoms*: `yum` task fails with package not found.  
*Solution*: Check yum repositories.  
```bash
ansible stapp02 -i inventory -m command -a "yum repolist"
```

### **Issue 2: Document Root Not Updated**  
*Symptoms*: `httpd` uses default `/var/www/html`.  
*Solution*: Verify `replace` tasks and regex patterns.  
```bash
ansible stapp02 -i inventory -m shell -a "grep DocumentRoot /etc/httpd/conf/httpd.conf"
ansible stapp02 -i inventory -m shell -a "grep '<Directory' /etc/httpd/conf/httpd.conf"
```

### **Issue 3: Directory Missing**  
*Symptoms*: `/var/www/html/myroot` not found.  
*Solution*: Ensure `file` task creates the directory.  
```bash
ansible stapp02 -i inventory -m shell -a "ls -ld /var/www/html/myroot"
```

### **Issue 4: Template Not Found**  
*Symptoms*: `template` task fails with file not found.  
*Solution*: Verify template path.  
```bash
ls /home/thor/playbooks/templates/phpinfo.php.j2
```

### **Issue 5: PHP Not Rendering**  
*Symptoms*: `curl http://localhost/myroot/phpinfo.php` returns raw PHP code or errors.  
*Solution*: Ensure `php` module is loaded and `httpd` is restarted.  
```bash
ansible stapp02 -i inventory -m shell -a "httpd -M | grep php"
ansible stapp02 -i inventory -m shell -a "sudo systemctl restart httpd"
```

### **Issue 6: Service Not Running**  
*Symptoms*: `httpd` fails to start.  
*Solution*: Check logs for errors (e.g., SELinux, port conflicts).  
```bash
ansible stapp02 -i inventory -m shell -a "journalctl -u httpd"
ansible stapp02 -i inventory -m shell -a "sudo getenforce"
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Enable SELinux for Apache in production (`httpd_t` context for `/var/www/html/myroot`).  
  ```bash
  ansible stapp02 -i inventory -m shell -a "sudo chcon -R -t httpd_sys_content_t /var/www/html/myroot"
  ```  
- *Backup*: Back up `/etc/httpd/conf/httpd.conf` before modification.  
  ```bash
  ansible stapp02 -i inventory -m copy -a "src=/etc/httpd/conf/httpd.conf dest=/etc/httpd/conf/httpd.conf.bak"
  ```  
- *Idempotency*: All modules (`yum`, `file`, `replace`, `template`, `service`) ensure safe re-runs.  
- *Verbose Output*: Use `-v` for debugging:  
  ```bash
  ansible-playbook -i inventory httpd.yml -v
  ```  
- *PHP Testing*: Use `curl` to verify PHP output or test in a browser if accessible.  

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Document Root Configuration*: Updating both `DocumentRoot` and `<Directory>` sections in `httpd.conf`.  
2. *PHP Integration*: Ensuring PHP is properly installed and loaded.  
3. *Template Deployment*: Deploying `phpinfo.php.j2` correctly with proper permissions.  

### **Solution Approach**  
1. Used two `replace` tasks to update `DocumentRoot` and `<Directory>` in `httpd.conf`.  
2. Installed `php` package and verified module loading with `httpd -M`.  
3. Created and deployed `phpinfo.php.j2` using the `template` module with `apache:apache` ownership.  

### **Key Success Factors**  
- *Inventory*: Verified `stapp02` details.  
- *Template*: Ensured `phpinfo.php.j2` contains valid PHP code.  
- *Verification*: Checked service status, file deployment, and PHP output.  

### **Critical Details**  
- *Inventory*: `/home/thor/playbooks/inventory`  
- *Playbook*: `/home/thor/playbooks/httpd.yml`  
- *Template*: `/home/thor/playbooks/templates/phpinfo.php.j2`  
- *Files*: `/var/www/html/myroot/phpinfo.php`, `/etc/httpd/conf/httpd.conf`  
- *Service*: `httpd`  

### **Ansible Benefits**  
- *Automation*: Simplifies package installation, configuration, and file deployment.  
- *Consistency*: Ensures uniform setup on `stapp02`.  
- *Reusability*: Playbook can be adapted for other servers.  

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Configure `httpd` with SSL/TLS and disable directory indexing.  
  ```bash
  ansible stapp02 -i inventory -m lineinfile -a "path=/etc/httpd/conf/httpd.conf line='Options -Indexes' insertafter='^<Directory \"/var/www/html/myroot\">'"
  ```  
- 📊 *Logging*: Enable Apache and Ansible logging for auditing.  
  ```bash
  export ANSIBLE_LOG_PATH=/var/log/ansible.log
  ansible stapp02 -i inventory -m command -a "tail /var/log/httpd/access_log"
  ```  
- 🛡️ *Permissions*: Restrict `/var/www/html/myroot` access in production.  
- 🌐 *Scalability*: Extend playbook to additional servers by updating `hosts` in the playbook.  
- ⚠️ *PHP Security*: Disable dangerous PHP functions in `php.ini` (e.g., `exec`, `system`).  
  ```bash
  ansible stapp02 -i inventory -m lineinfile -a "path=/etc/php.ini line='disable_functions = exec,system' insertafter='^;disable_functions'"
  ```  

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Replace Module*: Modifying configuration files with precise regex patterns.  
- *Template Module*: Deploying dynamic files with Jinja2 templates.  
- *PHP Integration*: Setting up and verifying PHP with Apache.  
- *Service Management*: Starting and enabling services with Ansible.  

**Ansible Features Used**:  
- `yum` for package installation.  
- `file` for directory creation.  
- `replace` for configuration file updates.  
- `template` for dynamic file deployment.  
- `service` for managing `httpd`.  

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Inventory*: Verified `/home/thor/playbooks/inventory` for `stapp02`.  
- *Template*: Created `/home/thor/playbooks/templates/phpinfo.php.j2`.  
- *Playbook*: Created `/home/thor/playbooks/httpd.yml` to install `httpd` and `php`, configure document root, and deploy `phpinfo.php`.  
- *Execution*: Ran successfully.  
- *Verification*: Confirmed service status, document root, file deployment, and PHP functionality.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: Apache and PHP are configured on `stapp02` with a custom document root at `/var/www/html/myroot`, and `phpinfo.php` is accessible, confirming PHP functionality.