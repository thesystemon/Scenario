### **Scenario: Developer Group Setup**

#### **1. Create the Group**
Create a group called `developer`:
```bash
sudo groupadd developer
```

---

#### **2. Create 10 Users and Add Them to the `developer` Group**
Create 10 users (`user1` to `user10`) and assign them to the `developer` group:

```bash
for i in {1..10}; do
    sudo useradd -m -s /bin/bash -G developer user$i
    echo "user$i:password$i" | sudo chpasswd
done
```
- Each user gets a password (e.g., `password1` for `user1`).
- The `-G developer` flag ensures they are added to the `developer` group.

---

#### **3. Create the `code.txt` File**
Create a file (`code.txt`) in a shared directory and assign it to the `developer` group:

1. **Create the directory and file:**
   ```bash
   sudo mkdir /shared
   sudo touch /shared/code.txt
   ```

2. **Set ownership and group:**
   ```bash
   sudo chown :developer /shared/code.txt
   sudo chmod 660 /shared/code.txt
   ```
   - `660`: Owner and group can read and write. Others have no access.

---

#### **4. Assign Permissions Using ACL**

1. **Grant Read-Only Access to `user1` to `user5`:**
   Use `setfacl` to limit these users to read-only access:
   ```bash
   for i in {1..5}; do
       sudo setfacl -m u:user$i:r-- /shared/code.txt
   done
   ```

2. **Grant Read-Write Access to `user6` to `user10`:**
   Use `setfacl` to grant these users both read and write access:
   ```bash
   for i in {6..10}; do
       sudo setfacl -m u:user$i:rw- /shared/code.txt
   done
   ```

---

#### **5. Verify ACL Permissions**
Check the current ACL permissions for `code.txt`:
```bash
getfacl /shared/code.txt
```

You should see output like this:
```
user:user1:r--
user:user2:r--
user:user3:r--
user:user4:r--
user:user5:r--
user:user6:rw-
user:user7:rw-
user:user8:rw-
user:user9:rw-
user:user10:rw-
group:developer:rw-
```

---

#### **6. Test the Permissions**
1. **Log in as `user1` to `user5` (read-only users):**
   ```bash
   su - user1
   cat /shared/code.txt  # Should work
   echo "Test" >> /shared/code.txt  # Should fail
   ```

2. **Log in as `user6` to `user10` (read-write users):**
   ```bash
   su - user6
   echo "Hello, Developer Team!" >> /shared/code.txt  # Should work
   ```

---

### **Scenario Summary**
1. The group is named `developer`.
2. The file `/shared/code.txt` is shared among users in the `developer` group.
3. Users `user1` to `user5` have read-only access to the file.
4. Users `user6` to `user10` have read and write access.
