# John the Ripper — Guida ai Principali Comandi e Test

> **Avviso legale:** John the Ripper è uno strumento per il cracking di password. Utilizzarlo esclusivamente su hash di cui si possiede l'autorizzazione esplicita. L'uso non autorizzato è illegale.

---

## 1. Installazione

### Su Kali Linux (già incluso)
```bash
john --version
```

### Installare la versione Jumbo (più formati supportati)
```bash
apt update && apt install -y john
```

---

## 2. Sintassi generale

```
john [opzioni] <file-hash>
```

---

## 3. Cracking di base

### Cracking automatico (modalità default)
```bash
john hashes.txt
```

> John rileva automaticamente il formato dell'hash e prova prima con wordlist, poi con regole, poi con brute force.

### Mostrare le password trovate
```bash
john --show hashes.txt
```

### Mostrare le password trovate per un formato specifico
```bash
john --show --format=md5 hashes.txt
```

---

## 4. Specificare il formato dell'hash

### Elencare tutti i formati supportati
```bash
john --list=formats
```

### Specificare il formato manualmente
```bash
john --format=md5 hashes.txt
john --format=sha256 hashes.txt
john --format=bcrypt hashes.txt
john --format=nt hashes.txt          # hash NTLM Windows
john --format=sha512crypt hashes.txt # /etc/shadow Linux
john --format=zip hashes.txt         # file ZIP protetti
john --format=pdf hashes.txt         # PDF protetti
```

---

## 5. Modalità di attacco

### Modalità wordlist (dizionario)
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

### Modalità wordlist con regole di trasformazione
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --rules hashes.txt
```

### Modalità single (usa le informazioni dell'utente come base)
```bash
john --single hashes.txt
```

### Modalità incremental (brute force completo)
```bash
john --incremental hashes.txt
```

### Modalità incremental solo con cifre
```bash
john --incremental=digits hashes.txt
```

### Modalità incremental solo con lettere
```bash
john --incremental=alpha hashes.txt
```

### Modalità Markov (statisticamente ottimizzata)
```bash
john --markov hashes.txt
```

---

## 6. Regole personalizzate

### Usare un set di regole specifico
```bash
john --wordlist=passwords.txt --rules=jumbo hashes.txt
```

### Elencare i set di regole disponibili
```bash
john --list=rules
```

---

## 7. Gestione delle sessioni

### Avviare una sessione con nome
```bash
john --session=sessione1 --wordlist=rockyou.txt hashes.txt
```

### Riprendere una sessione interrotta
```bash
john --restore=sessione1
```

### Riprendere l'ultima sessione
```bash
john --restore
```

---

## 8. Estrarre hash da file di sistema

### Estrarre hash da /etc/passwd e /etc/shadow (Linux)
```bash
unshadow /etc/passwd /etc/shadow > hash-linux.txt
john hash-linux.txt
```

### Estrarre hash NTLM da Windows (con samdump2)
```bash
samdump2 /mnt/windows/system32/config/SYSTEM /mnt/windows/system32/config/SAM > hash-windows.txt
john --format=nt hash-windows.txt
```

---

## 9. Cracking di file protetti

### Estrarre l'hash da un file ZIP protetto
```bash
zip2john archivio.zip > hash-zip.txt
john hash-zip.txt
```

### Estrarre l'hash da un file RAR protetto
```bash
rar2john archivio.rar > hash-rar.txt
john hash-rar.txt
```

### Estrarre l'hash da un file PDF protetto
```bash
pdf2john documento.pdf > hash-pdf.txt
john hash-pdf.txt
```

### Estrarre l'hash da una chiave SSH privata protetta
```bash
ssh2john id_rsa > hash-ssh.txt
john hash-ssh.txt
```

### Estrarre l'hash da un file Office protetto
```bash
office2john documento.docx > hash-office.txt
john hash-office.txt
```

### Estrarre l'hash da un volume KeePass
```bash
keepass2john database.kdbx > hash-keepass.txt
john hash-keepass.txt
```

---

## 10. Opzioni avanzate

### Limitare la lunghezza della password nel brute force
```bash
john --incremental --min-length=4 --max-length=8 hashes.txt
```

### Usare più thread (CPU)
```bash
john --wordlist=rockyou.txt --fork=4 hashes.txt
```

### Impostare il charset per il brute force
```bash
john --incremental=Lower hashes.txt   # solo minuscole
john --incremental=Upper hashes.txt   # solo maiuscole
john --incremental=Digits hashes.txt  # solo numeri
john --incremental=Alnum hashes.txt   # alfanumerico
```

### Eseguire il crack solo su un utente specifico
```bash
john --user=root hashes.txt
```

---

## 11. Esempi completi per lab

### Crack hash Linux (/etc/shadow)
```bash
unshadow /etc/passwd /etc/shadow > shadow-hashes.txt
john --wordlist=/usr/share/wordlists/rockyou.txt shadow-hashes.txt
john --show shadow-hashes.txt
```

### Crack hash NTLM Windows
```bash
john --format=nt --wordlist=/usr/share/wordlists/rockyou.txt hash-nt.txt
john --show --format=nt hash-nt.txt
```

### Crack file ZIP con wordlist e regole
```bash
zip2john segreto.zip > hash-zip.txt
john --wordlist=/usr/share/wordlists/rockyou.txt --rules hash-zip.txt
john --show hash-zip.txt
```

### Brute force su hash MD5 (password corte)
```bash
john --format=md5 --incremental --min-length=1 --max-length=6 hashes.txt
```
