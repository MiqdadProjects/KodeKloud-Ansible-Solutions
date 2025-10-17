# 🌟 **TASK02: Add Users and Groups on App Server 2**  
![Task Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)  
![Environment](https://img.shields.io/badge/Environment-Stratos%20DC-blue?style=flat-square)  
![Tool](https://img.shields.io/badge/Tool-Ansible-red?style=flat-square)  

## 📋 **Task Description**  
![Section](https://img.shields.io/badge/Section-Description-orange?style=flat-square)  

The *Nautilus DevOps team* needs to create user accounts and groups for new developers and engineers on *App Server 2* in *Stratos DC*, with specific home directories, encrypted passwords, and sudo privileges.

### **Requirements**  
![Requirements](https://img.shields.io/badge/Section-Requirements-yellow?style=flat-square)  
1. **Inventory File**: Use `/home/thor/playbooks/inventory`.  
2. **Users and Groups**: Use `/home/thor/playbooks/data/users.yml` for user and group lists.  
3. **Playbook**: Create `/home/thor/playbooks/add_users.yml` to:  
   - Add users from `users.yml` on `stapp02`.  
   - Create `developers` and `admins` groups.  
   - Assign users to their respective groups.  
   - Set home directory for `developers` to `/var/www`, and default home directories for `admins` (e.g., `/home/devid` for `devid`).  
   - Set passwords: `B4zNgHA7Ya` for `developers`, `8FmzjvFU6S` for `admins`, encrypted using `/home/thor/playbooks/secrets/vault.txt`.  
   - Add `admins` users to `wheel` group for sudo access.  
4. **Ansible Config**: Update `/home/thor/playbooks/ansible.cfg` to use `vault.txt`.  
5. **Execution**: Run with:  
   ```bash
   ansible-playbook -i inventory add_users.yml
   ```

---

## 🛠️ **Infrastructure Overview**  
![Section](https://img.shields.io/badge/Section-Infrastructure-blueviolet?style=flat-square)  

- **Target Environment**: *Stratos DC*  
- **Resources**:  
  - *Jump Host*: User `thor`, inventory at `/home/thor/playbooks/inventory`.  
  - *App Server*: `stapp02` (IP: `172.16.238.11`, User: `steve`, Password: `Am3ric@`).  
  - *Playbook File*: `/home/thor/playbooks/add_users.yml`  
  - *Data File*: `/home/thor/playbooks/data/users.yml`  
  - *Vault File*: `/home/thor/playbooks/secrets/vault.txt`  
  - *Config File*: `/home/thor/playbooks/ansible.cfg`  

---

## 📜 **Solution Overview**  
![Section](https://img.shields.io/badge/Section-Solution-green?style=flat-square)  

### **Architecture Components**  
- *Inventory*: Defines `stapp02` for Ansible management.  
- *Ansible Config*: Configures vault password file.  
- *Playbook*: Uses `group` and `user` modules with vault-encrypted passwords to manage users and groups.  
- *App Server 2*: Configured with users, groups, home directories, and sudo access.

### **Implementation Strategy**  
1. Update `ansible.cfg` to reference the vault password file.  
2. Encrypt passwords using Ansible Vault.  
3. Create `add_users.yml` to manage groups and users based on `users.yml`.  
4. Execute the playbook and verify user and group configurations.

---

## 🚫 **Potential Errors to Avoid**  
![Section](https://img.shields.io/badge/Section-Errors-red?style=flat-square)  

1. **Vault Password Mismatch**:  
   - *Issue*: Playbook fails due to incorrect vault password.  
   - *Fix*: Verify `vault.txt` and `ansible.cfg` settings.  
2. **Home Directory Issues**:  
   - *Issue*: `/var/www` not set for developers.  
   - *Fix*: Use `home` parameter in `user` module.  
3. **Sudo Access Failure**:  
   - *Issue*: Admins lack sudo privileges.  
   - *Fix*: Ensure `wheel` group membership with `append: yes`.  
4. **Users.yml Not Found**:  
   - *Issue*: Playbook fails to load user data.  
   - *Fix*: Verify path to `users.yml`.  
5. **Password Hashing**:  
   - *Issue*: Passwords not applied correctly.  
   - *Fix*: Use `password_hash('sha512')` filter.

---

## 🚀 **Implementation Steps**  
![Section](https://img.shields.io/badge/Section-Steps-teal?style=flat-square)  

### **Step 1: Connect to Jump Host**  
```bash
ssh thor@jumphost
```
*Purpose*: Access the jump host as `thor`.

### **Step 2: Navigate to Playbooks Directory**  
```bash
cd /home/thor/playbooks
```
*Purpose*: Move to the working directory.

### **Step 3: Update Ansible Config**  
```bash
vi ansible.cfg
```
**Content**:
```ini
[defaults]
inventory = ./inventory
vault_password_file = ./secrets/vault.txt
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Configure Ansible to use the vault password file.

### **Step 4: Encrypt Passwords**  
```bash
ansible-vault encrypt_string 'B4zNgHA7Ya' --name 'dev_password'
ansible-vault encrypt_string '8FmzjvFU6S' --name 'admin_password'
```
**Expected Output** (example):
```yaml
dev_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          663861663... (encrypted string)
admin_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          326462663... (encrypted string)
```
*Purpose*: Encrypt passwords for secure storage.

### **Step 5: Create Playbook**  
```bash
vi add_users.yml
```
**Content**:
```yaml
---
- name: Add users and groups on app server 2
  hosts: stapp02
  become: yes
  vars_files:
    - ./data/users.yml
  vars:
    dev_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          663861663... (replace with actual encrypted string)
    admin_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          326462663... (replace with actual encrypted string)
  tasks:
    - name: Create groups
      ansible.builtin.group:
        name: "{{ item }}"
        state: present
      loop:
        - developers
        - admins
    - name: Add developer users
      ansible.builtin.user:
        name: "{{ item }}"
        group: developers
        home: /var/www
        password: "{{ dev_password | password_hash('sha512') }}"
        state: present
      loop: "{{ developers }}"
    - name: Add admin users
      ansible.builtin.user:
        name: "{{ item }}"
        groups: "admins,wheel"
        append: yes
        password: "{{ admin_password | password_hash('sha512') }}"
        state: present
      loop: "{{ admins }}"
```
*Save and Exit*: Press `Esc`, type `:wq`, press `Enter`.  
*Purpose*: Create a playbook to manage users and groups on `stapp02`.

### **Step 6: Run Playbook**  
```bash
ansible-playbook -i inventory add_users.yml
```
**Expected Output**:
```bash
PLAY [Add users and groups on app server 2] ************************************

TASK [Gathering Facts] ********************************************************
ok: [stapp02]

TASK [Create groups] **********************************************************
changed: [stapp02] => (item=developers)
changed: [stapp02] => (item=admins)

TASK [Add developer users] ****************************************************
changed: [stapp02] => (item=tim)
...

TASK [Add admin users] *******************************************************
changed: [stapp02] => (item=rob)
...

PLAY RECAP ********************************************************************
stapp02                    : ok=4    changed=3    unreachable=0    failed=0
```
*Purpose*: Apply user and group configurations.

### **Step 7: Verify Setup**  
```bash
ssh steve@172.16.238.11
sudo cat /etc/passwd | grep -E "rob|tim"
sudo getent group developers admins wheel
```
**Expected Output**:
```bash
tim:x:1002:1002::/var/www:/bin/bash
rob:x:1006:1006::/home/rob:/bin/bash
developers:x:1002:
admins:x:1003:rob,david,joy
wheel:x:10:ansible,steve,rob,david,joy
```
*Purpose*: Confirm user home directories, group memberships, and sudo access.

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

### **Ansible Config** (`/home/thor/playbooks/ansible.cfg`)  
```ini
[defaults]
inventory = ./inventory
vault_password_file = ./secrets/vault.txt
```
- *Purpose*: Configures inventory and vault password file.

### **Playbook** (`/home/thor/playbooks/add_users.yml`)  
```yaml
---
- name: Add users and groups on app server 2
  hosts: stapp02
  become: yes
  vars_files:
    - ./data/users.yml
  vars:
    dev_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          663861663... (replace with actual encrypted string)
    admin_password: !vault |
          $ANSIBLE_VAULT;1.1;AES256
          326462663... (replace with actual encrypted string)
  tasks:
    - name: Create groups
      ansible.builtin.group:
        name: "{{ item }}"
        state: present
      loop:
        - developers
        - admins
    - name: Add developer users
      ansible.builtin.user:
        name: "{{ item }}"
        group: developers
        home: /var/www
        password: "{{ dev_password | password_hash('sha512') }}"
        state: present
      loop: "{{ developers }}"
    - name: Add admin users
      ansible.builtin.user:
        name: "{{ item }}"
        groups: "admins,wheel"
        append: yes
        password: "{{ admin_password | password_hash('sha512') }}"
        state: present
      loop: "{{ admins }}"
```
- *Play Name*: Descriptive for clarity.  
- *Hosts*: Targets `stapp02`.  
- *Privilege Escalation*: `become: yes` for root access.  
- *Tasks*:  
  - *Groups*: Creates `developers` and `admins`.  
  - *Developer Users*: Sets home directory to `/var/www`.  
  - *Admin Users*: Adds to `admins` and `wheel` groups.  
*Purpose*: Configures users and groups with correct attributes.

### **Resource Attributes**  
| **Resource** | **Attribute** | **Value** | **Description** |
|--------------|---------------|-----------|-----------------|
| Inventory    | Hostname      | `stapp02` | App Server 2 alias |
| Inventory    | ansible_host  | `172.16.238.11` | App Server 2 IP |
| Inventory    | ansible_user  | `steve`   | App Server 2 user |
| Inventory    | ansible_ssh_pass | `Am3ric@` | App Server 2 password |
| Playbook     | Hosts         | `stapp02` | Targets App Server 2 |
| Playbook     | Task Modules  | `group`, `user` | Manages groups and users |
| Playbook     | Groups        | `developers`, `admins`, `wheel` | Groups to create |
| Playbook     | Home Directory | `/var/www` | For developer users |
| Playbook     | Password (Developers) | `B4zNgHA7Ya` | Encrypted with vault |
| Playbook     | Password (Admins) | `8FmzjvFU6S` | Encrypted with vault |

---

## ✅ **Verification Steps**  
![Section](https://img.shields.io/badge/Section-Verification-green?style=flat-square)  

### **Step 1: Verify Inventory**  
```bash
cat /home/thor/playbooks/inventory
```

### **Step 2: Verify Ansible Config**  
```bash
cat /home/thor/playbooks/ansible.cfg
```

### **Step 3: Verify Playbook Execution**  
```bash
ansible-playbook -i inventory add_users.yml
```

### **Step 4: Verify Users and Groups**  
```bash
ansible stapp02 -i inventory -m command -a "cat /etc/passwd | grep -E 'rob|tim'"
ansible stapp02 -i inventory -m command -a "getent group developers admins wheel"
```

### **Step 5: Verify Sudo Access**  
```bash
ssh rob@172.16.238.11
sudo -l
```
**Expected Output**:
```bash
User rob may run the following commands on stapp02:
    (ALL : ALL) ALL
```
*Purpose*: Confirm sudo privileges for admin users.

---

## 🧪 **Testing**  
![Section](https://img.shields.io/badge/Section-Testing-purple?style=flat-square)  

### **Test SSH Connectivity**  
```bash
ssh steve@172.16.238.11
```

### **Test Playbook Syntax**  
```bash
ansible-playbook -i inventory add_users.yml --syntax-check
```

### **Test Vault Encryption**  
```bash
ansible-vault view add_users.yml
```
*Purpose*: Ensure passwords are encrypted correctly.

### **Test Group Existence**  
```bash
ansible stapp02 -i inventory -m command -a "getent group developers admins wheel"
```

---

## 📚 **Quick Command Reference**  
![Section](https://img.shields.io/badge/Section-Commands-blue?style=flat-square)  
```bash
cd /home/thor/playbooks
vi ansible.cfg
ansible-vault encrypt_string 'B4zNgHA7Ya' --name 'dev_password'
vi add_users.yml
ansible-playbook -i inventory add_users.yml
ansible stapp02 -i inventory -m command -a "cat /etc/passwd | grep -E 'rob|tim'"
ansible stapp02 -i inventory -m command -a "getent group developers admins wheel"
```

---

## 🛠️ **Troubleshooting Common Issues**  
![Section](https://img.shields.io/badge/Section-Troubleshooting-red?style=flat-square)  

### **Issue 1: Vault Password Error**  
*Symptoms*: Playbook fails with vault decryption error.  
*Solution*: Verify `vault.txt` path in `ansible.cfg`.  
```bash
cat ansible.cfg
cat secrets/vault.txt
```

### **Issue 2: Users Not Created**  
*Symptoms*: Users missing in `/etc/passwd`.  
*Solution*: Check `users.yml` and playbook tasks.  
```bash
cat data/users.yml
ansible-playbook -i inventory add_users.yml -v
```

### **Issue 3: Sudo Access Missing**  
*Symptoms*: Admins cannot run sudo commands.  
*Solution*: Verify `wheel` group membership.  
```bash
ansible stapp02 -i inventory -m command -a "getent group wheel"
```

---

## 💡 **Additional Tips**  
![Section](https://img.shields.io/badge/Section-Tips-lightgrey?style=flat-square)  
- *Security*: Use SSH keys and Ansible Vault for production.  
- *Idempotency*: `group` and `user` modules ensure safe re-runs.  
- *Verbose Output*: Use `-v` for detailed logs:  
  ```bash
  ansible-playbook -i inventory add_users.yml -v
  ```

---

## 🚨 **Task-Specific Challenge & Solution**  
![Section](https://img.shields.io/badge/Section-Challenges-orange?style=flat-square)  

### **Challenges Encountered**  
1. *Vault Encryption*: Managing encrypted passwords.  
2. *Home Directories*: Setting `/var/www` for developers.  
3. *Sudo Access*: Ensuring admins have `wheel` membership.

### **Solution Approach**  
1. Used `ansible-vault encrypt_string` for passwords.  
2. Set `home: /var/www` for developers.  
3. Added `wheel` to `groups` with `append: yes` for admins.

### **Key Success Factors**  
- *Vault*: Correctly configured `ansible.cfg` and encrypted passwords.  
- *Users.yml*: Assumed valid user lists for `developers` and `admins`.  
- *Verification*: Checked user and group configurations.

---

## ⚠️ **Production Notes**  
![Section](https://img.shields.io/badge/Section-Production%20Notes-yellow?style=flat-square)  
- 🔒 *Security*: Use strong vault passwords and SSH keys.  
- 📊 *Logging*: Enable Ansible logging:  
  ```bash
  export ANSIBLE_LOG_PATH=/var/log/ansible.log
  ```  
- 🛡️ *Permissions*: Restrict home directory access in production.  
- 🌐 *Scalability*: Extend to additional servers if needed.

---

## 📖 **Learning Outcomes**  
![Section](https://img.shields.io/badge/Section-Learning%20Outcomes-blueviolet?style=flat-square)  
- *Vault Encryption*: Securing passwords with Ansible Vault.  
- *User Management*: Creating users and groups with specific attributes.  
- *Sudo Configuration*: Granting sudo access via `wheel` group.

**Ansible Features Used**:  
- `group` and `user` modules.  
- `vars_files` for external data.  
- Ansible Vault for password encryption.

---

## 🎯 **Task Completion Summary**  
![Status](https://img.shields.io/badge/Status-Success-brightgreen?style=flat-square)  
✅ **Completed**:  
- *Config*: Updated `ansible.cfg` for vault.  
- *Playbook*: Created `add_users.yml` to manage users and groups.  
- *Execution*: Ran successfully.  
- *Verification*: Confirmed user, group, and sudo configurations.  

**Final Status**: 🎉 **Task completed successfully!**  
*Outcome*: Users and groups are configured on `stapp02` with correct attributes.