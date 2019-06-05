## Password Hashing and Cracking
Password is an important user authentication mechanism. Passwords are stored in the database as
hashes rather than plain text. If users’ passwords are stored in a database as plain text, malicious
hackers can get immediate access to them if they break into the system. In this lab, you will
understand what password hash is and how password hash be cracked.

## Password Hashing
A hash function turns any amount of data into a fixed-length “fingerprint” that cannot be reversed.  
The fixed-length “fingerprint” is called a hash value or hash code. 
The unique feature of hash functions is that any change in input data will result in a completely different hash code. Cryptographic hash functions are used to implement password hashing.There are multiplecryptographic hash functions, such as MD5, SHA-2.  

For example, the following are the MD5 hash codes of different strings.  
MD5(“hello”) = “5D41402ABC4B2A76B9719D911017C592”  
MD5(“hollo”) = “181D1F65FC3EDFC75945B24F22CD7E22”  
MD5(“helloo”) = “B373870B9139BBADE83396A49B1AFC9A”  

### Task1: Exploring Linux Password Hashes

The users’ information and passwords in Linux and UNIX operating system are stored in files.
/etc/passwd stores user accounts, and the /etc/shadow stores the information about user accounts
and the encrypted password hashes. /etc/shadow has more restrictive permissions than the
/etc/passwd file. On most Linux systems, only the root account has the ability to read the
contents of the shadow file.  

**Step 1: View the /etc/passwd file. Open a terminal and type the following command**  
*cat /etc/passwd*  
Each entry is the password information for each user (or user account) of the system. For example:   

| root:    | x:       | 0:      | 0:       | root:         | /root:         | /bin/bash     |
|----------|----------|---------|----------|---------------|----------------|---------------|
| Username | password | User ID | Group ID | User ID info  | Home Directory | Command/shell |  

- **Username: it should be between 1 and 32 characters in length.**  
- **Password**: an ‘x’ character indicates that encrypted password is stored in /etc/shadow file.  
- **User ID (UID)**: Each user must be assigned a user ID (UID). UID 0 (zero) is reserved for root and UIDs
1-99 are reserved for other predefined accounts. Further UID 100-999 are reserved by system for
administrative and system accounts/groups.  
- **Group ID (GID)**:The primary group ID (stored in /etc/group file)   
- **User ID Info**: The comment field. It allows you to add extra information about the users such as user’s
full name, phone number etc.  
- **Home directory**: The absolute path to the directory the user will be in when they log in.  
- **Command/shell**: The absolute path of a command or shell (/bin/bash).  

**Please take a scrrenshot of the results!**  

**Step 2: View the permission of the passwd file using the command:**  
*ls -l /etc/passwd*  

| -                                                                     | rw-                                                          | r--                                                                              | r--                                                 |
|-----------------------------------------------------------------------|--------------------------------------------------------------|----------------------------------------------------------------------------------|-----------------------------------------------------|
| ‘-’ indicates a file  'd’ indicates a directory  ‘l’ indicates a link | Read, write and execute permissions forthe owner of the file | Read, write and execute permissions for the members of the group owning the file | Read, write and execute permissions for other users |

**Please take a screenshot of the results**  

**Step 3: View the /etc/shadow file using the command:**  
*sudo cat /etc/shadow*  

Each entry is the hashed password for each user (or user account) of the system. As with the passwd
file, each field in the shadow file is also separated with ":" colon characters, and are as follows:  

**root:$6$OCtu.M/v$fpnhbjkpA4S29lKZ2TzRsl6ArWyvu9eIfWfC0H98t8OoLPokE8.d7q54
cynb0BTtLgN.IlolE72npACz7Dr2p.:16983:0:99999:7:::**

- **Username**: a direct match to the username in the /etc/passwd file.  
- **Password**: encrypted password. A blank entry (eg. ::) indicates a password is not required to log in
(usually a bad idea), and a ‘*’ entry (eg. :*:) indicates the account has been disabled. The “!” symbol
(often called a bang) represents that fact the password has not been set. Usually password format is set to
**$id$salt$hash**. The $id is the algorithm used On GNU/Linux as follows: **$1$** is MD5, **$2a$** is Blowfish,**$2y$** is Blowfish, **$5$** is SHA-256, **$6$** is SHA-512  
- **Last change**: the number of days (since January 1, 1970) since the password was last changed.
- **Min**: the minimum number of days required between password changes. 0 indicates it may be changed at
any time.  
- **Max**: the number of days after which password must be changed. 99999 indicates user can keep his or her
password unchanged for many, many years.  
- **Warn**: the number of days to warn user of an expiring password (7 for a full week)
- **Inactive**: the number of days after password expires that account is disabled  
- **Expire**: the number of days since January 1, 1970 that an account has been disabled  
- **A reserved field for possible future use**   

**Please take a scrrenshot of the result!**  

**Step4: Create a new user named alice in the command:**  
*sudo useradd alice*  

**Step 5: Create a new user named bob using the command:**  
*sudo useradd bob*  

**Step 6: Now, view the changes made to the passwd file:**  
*cat /etc/passwd*  

**Please take a scrrenshot of the result!**  

**Step 7: Set alice’s password to apw123 using the following command:**
*sudo passwd alice*
Enter new UNIX password: (type in apw123)  
Retype new UNIX password: (type in apw123)  

**Step 8: Set bob’s password to bpw123 using the command:**  
*sudo passwd bob*
Enter new UNIX password: (type in bpw123)  
Retype new UNIX password: (type in bpw123)  

**Step 9: Examine the alterations to the shadow file by typing the following:**  
*cat tail /etc/shadow*  

**Please take a screenshot of the result.**   

## Cracking Hashes and Rainbow Table
Although the hashing algorithms cannot be reversed, password hashes could be cracked. Hackers
can generate hashes from a dictionary of strings that are commonly used as passwords. If hackers
gain access to a database of hashed passwords, they can calculate the hash code for each string in
the database and match it with the current hash code. If one in the database matches, the plaintext
password of that hash is known. This is so-called brute force dictionary attack.  

The brute force cracking described above is very time-consuming for calculating the hash code for
every string in the database. The opposite way is to pre-calculate all the hash codes for the strings
in the database and store the mapping of the hash codes to the strings. Then, hackers just need to
look up a hash in the mapping table to find the password. However, this method requires too much
space considering the large volume of strings that could be used as passwords. Considering the
time-memory tradeoff, “rainbow table” is a better method that takes a place in between. It is a pre-
commutated lookup table, but sacrifice hash cracking speed to make the lookup tables smaller.  

### Task 2: Simple Password Cracking
There are some online cracking tools. For example, CrackStation (https://crackstation.net/) is a
online websites for cracking simple password hashes.

Please use the CrackStation to crack the following password hashes:
**Hash 1: 6384E2B2184BCBF58ECCF10CA7A6563C**
What is the password? What hash algorithm is used?    

**Hash 2:4E40E8FFE0EE32FA53E139147ED559229A5930F89C2204706FC174BEB36210B3**  
What is the password? What hash algorithm is used?   

**Hash 3: 5994F091C5CBC05EE2DF38DA2C54EA5BE663D54E**  
What is the password? What hash algorithm is used?  

### Salt  

Rainbow tables reply on the assumption that each password is hashed in the exact the same way.
To defeat rainbow tables, “salt” is invented to randomize the hash for each password. Using salt,
when the same password is hashed twice, two different hash codes will be generated. Salt is usually
stored together with hash code in the user account file.  

### Task 3: Using John the Ripper for Password Cracking  
John the Ripper is one of the well-known fast password cracking tool that can crack passwords
through a dictionary attack or through the use of brute force. It can be downloaded free at
www.openwall.com/john/.   
**Step 1: Install John the Ripper using the following command:**  
*sudo apt-get install john*   +

**Step 2: Type the following command to attempt to crack the passwords with john:**  
*sudo john /etc/shadow*  

*Please take a screenshot of the results*  

**Step 3: Can you crack the following passwords?**  
emiller:3e05v.ztZ8LNE:15652:0:99999:7:::  

tanderson:$1$AqW8SRi1$Dd0m3hFyOI276/IHinecr0:15652:0:99999:7:::  

awilliams:mQK2Y4hWq0SvY:15652:0:99999:7:::  

mdavis:$5$i3uY6Gfp$ywzsyCNRs7kbKbN7Ad0SnGR7P6bVmMQ8iJ7008mrGHC:156
52:0:99999:7:::  

djameson:$6$iimf1wnL$T/0zG89BxF.qKzMyX7BZJCSye5x7wIQxox5dMMwWPdvpz
FMOs2YkknqHdMbbdxyBN7NNNBnAh/d7YY2fRRV3k0:15652:0:99999:7:::  

Open a terminal and type  
*gedit*  
Paste the above information in the text editor and click the Save button in the menu to save it to Desktop as ***“hashfile1.txt”*




































      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      
      







