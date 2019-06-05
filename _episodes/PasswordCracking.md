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

## Task1: Exploring Linux Password Hashes

The users’ information and passwords in Linux and UNIX operating system are stored in files.
/etc/passwd stores user accounts, and the /etc/shadow stores the information about user accounts
and the encrypted password hashes. /etc/shadow has more restrictive permissions than the
/etc/passwd file. On most Linux systems, only the root account has the ability to read the
contents of the shadow file.  

**Step 1: View the /etc/passwd file. Open a terminal and type the following command**  
*cat /etc/passwd*  
Each entry is the password information for each user (or user account) of the system. For example:  
| root: | X: | 0: | 0: | root: |/root: | /bin/bash |  
|-------|----|----|----|-------|-------|-----------|  
| UserName|password|User ID| UserID info| Home directory| Command/shell|
