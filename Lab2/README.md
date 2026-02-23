
# Lab 2: Automated SQL Injection & Database Enumeration

## 📌 Overview
This laboratory focuses on the identification and exploitation of SQL Injection (SQLi) vulnerabilities using automated penetration testing tools. The objective was to pivot from a manual syntax error discovery to a full database dump, exposing user credentials and system metadata.

## 🛠️ Infrastructure & Environment
A hybrid virtualization and containerization approach was used to optimize hardware resources:
- **Target:** DVWA (Damn Vulnerable Web Application) running in a **Docker container** on the host.
- **Attacker:** **Parrot OS Security Edition** running in KVM/QEMU (Virt-Manager).
- **Network Path:** Attacker (VM) -> Virtual Bridge Gateway (`192.168.122.1`) -> Target (Port 8080).



## 🛡️ Technical Implementation

### Phase 1: Manual Vulnerability Confirmation
Initial testing was performed by injecting a single quote (`'`) into the `id` parameter. The application returned a MySQL syntax error, confirming that user input was being directly interpreted by the database engine.

### Phase 2: Session-Aware Automated Exploitation
Since the target required authentication, the `PHPSESSID` and `security` cookies were extracted. **sqlmap** was then utilized to automate the enumeration.

**Command Used:**
```bash
sqlmap -u "[http://192.168.122.1:8080/vulnerabilities/sqli/?id=1&Submit=Submit#](http://192.168.122.1:8080/vulnerabilities/sqli/?id=1&Submit=Submit#)" --cookie="PHPSESSID=[REDACTED]; security=low" --dbs

```

### Phase 3: Data Exfiltration

Once the `dvwa` database was identified, I proceeded to dump the `users` table, successfully retrieving:

* Usernames
* MD5 Password Hashes
* User Metadata

## 📊 Evidence of Exploitation

The following screenshot demonstrates the final stage of the attack where **sqlmap** successfully cracked the MD5 hashes of users like `admin` and `gordonb`.


<p align="center">
  <img src="https://github.com/user-attachments/assets/c0a8bb00-6e90-41c3-8c18-f87b3cb2e4bc" alt="sqlmap database dump" width="800">
  <br>
  <b>Figure 1: sqlmap successfully extracting and cracking the users table.</b>
</p>


## 🚀 DevOps & Security Mitigation

1. **Use of Prepared Statements:** Implementing PDO (PHP Data Objects) to ensure the SQL engine treats input as data, not executable code.
2. **Database Hardening:** Ensuring the web application user has "Least Privilege" access to the database.
3. **WAF Integration:** Utilizing ModSecurity or AWS WAF to detect and block SQLi signatures (e.g., `UNION SELECT`).



### 🐳 3. The DevOps File: `docker-compose.yml`
Since your report mentions using Docker, include this file in the `Lab2-SQL-Injection` folder. It proves you understand how the target was deployed.

```yaml
version: '3'
services:
  dvwa:
    image: vulnerables/web-dvwa
    ports:
      - "8080:80"
    restart: on-failure
