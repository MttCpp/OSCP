#HTB 
# 1 - Enumeration
## Info
	Target IP:   10.129.181.21 - 10.129.196.13
	Host: Apache 2.4.38 Debian -p 80
	
	
### nmap
> $nmap-sV-p 0-10000-oN ~/T/nmap. txt 10.129.181.21

	Starting Nmap 7.92 ( https://nmap.org ) at 2022-04-08 21:24 CEST
	Nmap scan report for 10.129.181.21
	Host is up (0.061s latency).
	Not shown: 10000 closed tcp ports (conn-refused)
	PORT STATE SERVICE VERSION
	80/tcp open http Apache httpd 2.4.38 ( (Debian))
	Service detection performed. Please report any incorrect results at https://
	.org/submit/
	Nmap done: 1 IP address (1 host up) scanned in 13.31 seconds


### gobuster
>Sgobuster dir-w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt -u 10.129.181.21 
>.
>(Status: 301) [Size: 311] -> http://10.129.181.21/js/

This Apache server needs a robots.txt file blocking at least the js folder

# Main Issue
Class validation input

	´´´js
	Validate ]*/
	var input = $('. validate-input .input100') ;
	$('. validate-form').on('submit',function(){
	var check = true;
	---
	for (var i=0; i<input. length; i++) {
	if (validate (input[i]) == false) {
	showValidate(input[i]);
	check=false;


# Exploitation
Idea:
1. Gain an username with [[SQL injection]]
2. Brute force to gain access 
3. Shell Upload
4. PrivEsc to root

		PrivEsc: local exploit apache 2.4.38 Apache HTTP 2.4.17-2.4.38 Local Root Privilege Escalation

## 1. SQL injection
#SQL #Injection

	Attempt 1: using burpsuite to intercept the HTTP GET request from the website

![[POSTIntercetpAppointment.png]]
> Now I see that the input has URL encoding of special characters, this comes really useful when trying multiple SQL injections with BurpSuite

Clich here see how to exploit [[SQL injection]] with Burpsuit in detail

In a Real World scenario the process would looks like this:

	1. Send the intercepted request to the Intruder Tab
	2. Select the right place to position payload 
	3. Use an Injection wordlist as payload
	4. In ->Payload Processing options select the right encoding rules for special characters : URL-Encode key characters
	5. Run exploit

---
That tecnique doesn't seem to work, as the server responded the same for each request send

Searching for other vulnerabilities in the internet. Found this one:

Insert below SQL query:

	Username: admin'#

	password: &anything&

>Where a single quote( ' ) allows the script to search for the admin username. By adding the hashtag (#), we will comment out the rest of the query, which will make searching for a matching password for the specified username. However, since we have skipped the password search part of our query, the script will now only search if any entry exists with the username admin.

So we are going to use any password like admin123 and then click login:

Flag: 

	Congratulations!
	You flag is: e3d0796d002a446c0e622226f42e9672