# Wireshark in Kali Linux — Guida ai Principali Comandi e Test

> **Avviso legale:** Wireshark è uno strumento di analisi del traffico di rete. Utilizzarlo esclusivamente su reti di cui si possiede l'autorizzazione esplicita. L'intercettazione non autorizzata del traffico è illegale.

---

## 1. Installazione

### Su Kali Linux (già incluso)
```bash
wireshark --version
```

### Installazione manuale
```bash
apt update && apt install -y wireshark
```

### Avviare Wireshark in modalità grafica
```bash
wireshark
```

### Avviare con privilegi root (sconsigliato, alternativa sicura sotto)
```bash
sudo wireshark
```

### Alternativa sicura: aggiungere l'utente al gruppo wireshark
```bash
sudo usermod -aG wireshark $USER
newgrp wireshark
```

---

## 2. TShark — Wireshark da riga di comando

TShark è la versione CLI di Wireshark, ideale per l'uso in terminale e scripting.

### Verificare l'installazione
```bash
tshark --version
```

### Listare le interfacce di rete disponibili
```bash
tshark -D
```

---

## 3. Cattura del traffico

### Cattura sull'interfaccia eth0
```bash
tshark -i eth0
```

### Cattura sull'interfaccia di loopback
```bash
tshark -i lo
```

### Cattura su tutte le interfacce
```bash
tshark -i any
```

### Cattura e salva su file .pcap
```bash
tshark -i eth0 -w cattura.pcap
```

### Cattura un numero limitato di pacchetti (es. 100)
```bash
tshark -i eth0 -c 100
```

### Cattura per un tempo limitato (es. 30 secondi)
```bash
tshark -i eth0 -a duration:30 -w cattura.pcap
```

### Cattura con limite di dimensione file (es. 10 MB)
```bash
tshark -i eth0 -a filesize:10240 -w cattura.pcap
```

---

## 4. Lettura e analisi di file .pcap

### Leggere un file pcap
```bash
tshark -r cattura.pcap
```

### Leggere con dettagli verbosi
```bash
tshark -r cattura.pcap -V
```

### Mostrare solo i primi N pacchetti
```bash
tshark -r cattura.pcap -c 50
```

### Estrarre solo campi specifici (es. IP sorgente e destinazione)
```bash
tshark -r cattura.pcap -T fields -e ip.src -e ip.dst
```

### Estrarre IP + porta + protocollo
```bash
tshark -r cattura.pcap -T fields -e ip.src -e ip.dst -e tcp.srcport -e tcp.dstport -e _ws.col.Protocol
```

### Output in formato JSON
```bash
tshark -r cattura.pcap -T json
```

### Output in formato CSV
```bash
tshark -r cattura.pcap -T fields \
  -e frame.number -e ip.src -e ip.dst -e _ws.col.Protocol \
  -E separator=, -E header=y > output.csv
```

---

## 5. Filtri di cattura (Capture Filters — BPF)

I filtri di cattura vengono applicati **durante** la cattura e usano la sintassi BPF.

### Catturare solo traffico TCP
```bash
tshark -i eth0 -f "tcp"
```

### Catturare solo traffico UDP
```bash
tshark -i eth0 -f "udp"
```

### Catturare solo traffico su porta 80 (HTTP)
```bash
tshark -i eth0 -f "port 80"
```

### Catturare traffico da/verso un IP specifico
```bash
tshark -i eth0 -f "host 192.168.1.1"
```

### Catturare traffico tra due host
```bash
tshark -i eth0 -f "src host 192.168.1.10 and dst host 192.168.1.1"
```

### Catturare solo pacchetti ICMP (ping)
```bash
tshark -i eth0 -f "icmp"
```

### Catturare traffico DNS (porta 53)
```bash
tshark -i eth0 -f "port 53"
```

### Escludere un IP dalla cattura
```bash
tshark -i eth0 -f "not host 192.168.1.1"
```

---

## 6. Filtri di visualizzazione (Display Filters — Wireshark)

I filtri di visualizzazione vengono applicati **dopo** la cattura e usano la sintassi Wireshark.

### Filtrare per protocollo
```bash
tshark -r cattura.pcap -Y "http"
tshark -r cattura.pcap -Y "dns"
tshark -r cattura.pcap -Y "ftp"
tshark -r cattura.pcap -Y "ssh"
tshark -r cattura.pcap -Y "tcp"
tshark -r cattura.pcap -Y "udp"
tshark -r cattura.pcap -Y "icmp"
```

### Filtrare per IP sorgente
```bash
tshark -r cattura.pcap -Y "ip.src == 192.168.1.10"
```

### Filtrare per IP destinazione
```bash
tshark -r cattura.pcap -Y "ip.dst == 192.168.1.1"
```

### Filtrare per porta TCP
```bash
tshark -r cattura.pcap -Y "tcp.port == 443"
```

### Filtrare richieste HTTP GET
```bash
tshark -r cattura.pcap -Y "http.request.method == GET"
```

### Filtrare richieste HTTP POST
```bash
tshark -r cattura.pcap -Y "http.request.method == POST"
```

### Mostrare solo pacchetti con errori TCP
```bash
tshark -r cattura.pcap -Y "tcp.analysis.flags"
```

### Mostrare solo handshake TLS/SSL
```bash
tshark -r cattura.pcap -Y "tls.handshake"
```

### Filtrare query DNS
```bash
tshark -r cattura.pcap -Y "dns.flags.response == 0"
```

### Filtrare risposte DNS
```bash
tshark -r cattura.pcap -Y "dns.flags.response == 1"
```

### Combinare filtri con AND / OR
```bash
tshark -r cattura.pcap -Y "ip.src == 192.168.1.10 && tcp.port == 80"
tshark -r cattura.pcap -Y "http || ftp"
```

---

## 7. Test e analisi comuni

### Analizzare il traffico HTTP in chiaro (credenziali, URL)
```bash
tshark -i eth0 -Y "http" -T fields \
  -e http.host \
  -e http.request.uri \
  -e http.request.method
```

### Estrarre URL complete dalle richieste HTTP
```bash
tshark -r cattura.pcap -Y "http.request" -T fields \
  -e http.host -e http.request.uri
```

### Estrarre credenziali FTP
```bash
tshark -r cattura.pcap -Y "ftp" -T fields \
  -e ftp.request.command \
  -e ftp.request.arg
```

### Estrarre query DNS
```bash
tshark -r cattura.pcap -Y "dns.flags.response == 0" -T fields \
  -e dns.qry.name
```

### Mostrare le statistiche dei protocolli
```bash
tshark -r cattura.pcap -q -z io,phs
```

### Mostrare le statistiche delle conversazioni TCP
```bash
tshark -r cattura.pcap -q -z conv,tcp
```

### Mostrare gli endpoint IP più attivi
```bash
tshark -r cattura.pcap -q -z endpoints,ip
```

### Mostrare statistiche HTTP (richieste per host)
```bash
tshark -r cattura.pcap -q -z http,tree
```

### Analizzare ritardi e tempi di risposta HTTP
```bash
tshark -r cattura.pcap -q -z http,stat
```

---

## 8. Esportazione dati

### Esportare oggetti HTTP (file scaricati)
```bash
tshark -r cattura.pcap --export-objects http,./export-http
```

### Esportare oggetti SMB
```bash
tshark -r cattura.pcap --export-objects smb,./export-smb
```

### Convertire pcap in pcapng
```bash
editcap -F pcapng cattura.pcap cattura.pcapng
```

### Dividere un pcap in file più piccoli (ogni 1000 pacchetti)
```bash
editcap -c 1000 cattura.pcap split.pcap
```

### Filtrare e salvare un sottoinsieme di pacchetti
```bash
tshark -r cattura.pcap -Y "http" -w solo-http.pcap
```

---

## 9. Analisi avanzata con filtri combinati

### Rilevare scan di porte (SYN senza ACK)
```bash
tshark -r cattura.pcap -Y "tcp.flags.syn == 1 && tcp.flags.ack == 0"
```

### Rilevare traffico su porte non standard (non 80/443/22)
```bash
tshark -r cattura.pcap -Y "tcp.port != 80 && tcp.port != 443 && tcp.port != 22"
```

### Rilevare connessioni con molti RST (possibile scan/attacco)
```bash
tshark -r cattura.pcap -Y "tcp.flags.reset == 1"
```

### Rilevare ARP spoofing (risposte ARP non richieste)
```bash
tshark -r cattura.pcap -Y "arp.opcode == 2"
```

### Monitorare traffico ICMP in tempo reale
```bash
tshark -i eth0 -Y "icmp" -T fields \
  -e ip.src -e ip.dst -e icmp.type
```

---

## 10. Utilizzo con Wireshark GUI (scorciatoie utili)

| Azione                          | Scorciatoia          |
|---------------------------------|----------------------|
| Avvia cattura                   | `Ctrl + E`           |
| Ferma cattura                   | `Ctrl + E`           |
| Apri file pcap                  | `Ctrl + O`           |
| Salva cattura                   | `Ctrl + S`           |
| Applica filtro di visualizzazione | `Invio` nella barra |
| Segui flusso TCP                | Click dx → *Follow → TCP Stream* |
| Segui flusso HTTP               | Click dx → *Follow → HTTP Stream* |
| Esporta oggetti HTTP            | *File → Export Objects → HTTP* |
| Statistiche protocolli          | *Statistics → Protocol Hierarchy* |
| Statistiche conversazioni       | *Statistics → Conversations* |
| Colorazione pacchetti           | *View → Coloring Rules* |

---

## 11. Comandi di supporto utili

### Unire più file pcap in uno
```bash
mergecap -w merged.pcap file1.pcap file2.pcap
```

### Mostrare info su un file pcap
```bash
capinfos cattura.pcap
```

### Verificare la validità di un file pcap
```bash
capinfos -M cattura.pcap
```

### Riordinare i pacchetti per timestamp
```bash
reordercap cattura.pcap cattura-ordinata.pcap
```
