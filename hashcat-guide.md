# Hashcat — Guida ai Principali Comandi e Test

> **Avviso legale:** Hashcat è uno strumento per il cracking di password tramite GPU. Utilizzarlo esclusivamente su hash di cui si possiede l'autorizzazione esplicita. L'uso non autorizzato è illegale.

---

## 1. Installazione

### Su Kali Linux (già incluso)
```bash
hashcat --version
```

### Installazione manuale
```bash
apt update && apt install -y hashcat
```

---

## 2. Sintassi generale

```
hashcat -m <hash-type> -a <attack-mode> <hash-file> [wordlist/mask]
```

---

## 3. Tipi di hash più comuni (`-m`)

| Codice | Tipo di hash                        |
|--------|-------------------------------------|
| `0`    | MD5                                 |
| `100`  | SHA1                                |
| `1400` | SHA256                              |
| `1700` | SHA512                              |
| `900`  | MD4                                 |
| `1000` | NTLM (Windows)                      |
| `3000` | LM (Windows)                        |
| `5600` | NetNTLMv2                           |
| `1800` | SHA512crypt (`/etc/shadow` Linux)   |
| `500`  | MD5crypt (`/etc/shadow` Linux)      |
| `3200` | bcrypt                              |
| `2500` | WPA/WPA2 (handshake)                |
| `22000`| WPA-PBKDF2-PMKID+EAPOL              |
| `7400` | sha256crypt                         |
| `13100`| Kerberos 5 TGS (AS-REP)            |

### Cercare il codice per tipo di hash
```bash
hashcat --help | grep -i md5
hashcat --example-hashes | grep -i ntlm
```

---

## 4. Modalità di attacco (`-a`)

| Codice | Modalità              |
|--------|-----------------------|
| `0`    | Wordlist (dizionario) |
| `1`    | Combinazione          |
| `3`    | Brute force (mask)    |
| `6`    | Wordlist + Mask       |
| `7`    | Mask + Wordlist       |

---

## 5. Attacco con wordlist (modalità 0)

### Attacco dizionario base
```bash
hashcat -m 0 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
```

### Con regole di trasformazione
```bash
hashcat -m 0 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

### Usare più wordlist
```bash
hashcat -m 0 -a 0 hashes.txt wordlist1.txt wordlist2.txt
```

---

## 6. Attacco brute force con mask (modalità 3)

### Caratteri speciali per le mask

| Simbolo | Charset                             |
|---------|-------------------------------------|
| `?l`    | Lettere minuscole (a-z)             |
| `?u`    | Lettere maiuscole (A-Z)             |
| `?d`    | Cifre (0-9)                         |
| `?s`    | Caratteri speciali                  |
| `?a`    | Tutti i caratteri (`?l?u?d?s`)      |
| `?b`    | Tutti i byte (0x00-0xff)            |

### Password di 8 cifre (PIN)
```bash
hashcat -m 0 -a 3 hashes.txt ?d?d?d?d?d?d?d?d
```

### Password di 6 caratteri alfanumerici
```bash
hashcat -m 0 -a 3 hashes.txt ?a?a?a?a?a?a
```

### Password con prefisso fisso + 4 cifre (es. Pass1234)
```bash
hashcat -m 0 -a 3 hashes.txt Pass?d?d?d?d
```

### Incrementale (lunghezza variabile da 1 a 8)
```bash
hashcat -m 0 -a 3 hashes.txt --increment --increment-min=1 --increment-max=8 ?a?a?a?a?a?a?a?a
```

---

## 7. Attacco a combinazione (modalità 1)

Combina ogni parola della prima lista con ogni parola della seconda:

```bash
hashcat -m 0 -a 1 hashes.txt wordlist1.txt wordlist2.txt
```

---

## 8. Attacco ibrido Wordlist + Mask (modalità 6)

Aggiunge il pattern mask alla fine di ogni parola della wordlist:

```bash
# es. password + 2 cifre → password12
hashcat -m 0 -a 6 hashes.txt /usr/share/wordlists/rockyou.txt ?d?d
```

---

## 9. Attacco ibrido Mask + Wordlist (modalità 7)

Aggiunge il pattern mask all'inizio di ogni parola:

```bash
# es. 2 cifre + password → 12password
hashcat -m 0 -a 7 hashes.txt ?d?d /usr/share/wordlists/rockyou.txt
```

---

## 10. Regole

### Elencare le regole disponibili
```bash
ls /usr/share/hashcat/rules/
```

### Regole comuni
```bash
hashcat -m 0 -a 0 hashes.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule
hashcat -m 0 -a 0 hashes.txt rockyou.txt -r /usr/share/hashcat/rules/rockyou-30000.rule
hashcat -m 0 -a 0 hashes.txt rockyou.txt -r /usr/share/hashcat/rules/d3ad0ne.rule
hashcat -m 0 -a 0 hashes.txt rockyou.txt -r /usr/share/hashcat/rules/dive.rule
```

---

## 11. Opzioni di performance

### Utilizzare la GPU (default su Hashcat)
```bash
hashcat -m 0 -a 0 hashes.txt rockyou.txt -d 1
```

### Listare i dispositivi disponibili (CPU/GPU)
```bash
hashcat -I
```

### Impostare il workload (1=low, 2=default, 3=high, 4=nightmare)
```bash
hashcat -m 0 -a 0 hashes.txt rockyou.txt -w 3
```

### Usare solo la CPU
```bash
hashcat -m 0 -a 0 hashes.txt rockyou.txt --opencl-device-types 1
```

---

## 12. Gestione sessioni e output

### Avviare con nome sessione
```bash
hashcat -m 0 -a 0 hashes.txt rockyou.txt --session=sessione1
```

### Riprendere una sessione interrotta
```bash
hashcat --session=sessione1 --restore
```

### Salvare i risultati su file
```bash
hashcat -m 0 -a 0 hashes.txt rockyou.txt -o risultati.txt
```

### Mostrare le password già craccate
```bash
hashcat -m 0 hashes.txt --show
```

### Modalità verbose (mostra tentativi)
```bash
hashcat -m 0 -a 0 hashes.txt rockyou.txt --debug-mode=1
```

---

## 13. Esempi completi per lab

### Crack hash MD5 con rockyou
```bash
hashcat -m 0 -a 0 md5-hashes.txt /usr/share/wordlists/rockyou.txt -o cracked.txt
hashcat -m 0 md5-hashes.txt --show
```

### Crack hash NTLM Windows
```bash
hashcat -m 1000 -a 0 ntlm-hashes.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```

### Crack WPA2 handshake
```bash
hashcat -m 22000 -a 0 handshake.hc22000 /usr/share/wordlists/rockyou.txt
```

### Crack NetNTLMv2 (da Responder)
```bash
hashcat -m 5600 -a 0 netntlmv2.txt /usr/share/wordlists/rockyou.txt
```

### Crack bcrypt con brute force (password corte)
```bash
hashcat -m 3200 -a 3 bcrypt-hashes.txt ?a?a?a?a?a -w 3
```
