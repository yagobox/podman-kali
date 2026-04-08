# Kali Linux con Podman — Guida Comandi

## 1. Installazione e configurazione iniziale

### Scaricare l'immagine ufficiale di Kali Linux
```bash
podman pull kalilinux/kali-rolling
```

### Verificare che l'immagine sia stata scaricata
```bash
podman images
```

---

## 2. Avvio del container

### Avvio interattivo (sessione bash)
```bash
podman run -it --name kali kalilinux/kali-rolling /bin/bash
```

### Avvio con cartella condivisa tra host e container
```bash
podman run -it --name kali -v ~/kali-shared:/root/shared kalilinux/kali-rolling /bin/bash
```

### Avvio con accesso alla rete dell'host
```bash
podman run -it --name kali --network host kalilinux/kali-rolling /bin/bash
```

### Avvio con più opzioni combinate (cartella condivisa + rete host + privilegi)
```bash
podman run -it --name kali \
  --network host \
  --privileged \
  -v ~/kali-shared:/root/shared \
  kalilinux/kali-rolling /bin/bash
```

---

## 3. Gestione del container esistente

### Avviare un container già esistente (ma fermo)
```bash
podman start kali
```

### Rientrare in un container già avviato
```bash
podman exec -it kali /bin/bash
```

### Fermare il container
```bash
podman stop kali
```

### Riavviare il container
```bash
podman restart kali
```

### Eliminare il container
```bash
podman rm kali
```

### Eliminare il container forzatamente (anche se in esecuzione)
```bash
podman rm -f kali
```

---

## 4. Monitoraggio

### Vedere tutti i container (attivi e fermi)
```bash
podman ps -a
```

### Vedere solo i container attivi
```bash
podman ps
```

### Vedere i log del container
```bash
podman logs kali
```

### Ispezionare il container (dettagli configurazione)
```bash
podman inspect kali
```

---

## 5. Aggiornare Kali Linux all'interno del container

```bash
apt update && apt upgrade -y
```

### Installare il meta-pacchetto con i tool principali
```bash
apt install -y kali-linux-headless
```

---

## 6. Risoluzione errori comuni

---

### Errore: container già attivo in un'altra sessione
**Messaggio:** `Error: container kali already exists`  
o  
`Error: container is already running`

**Soluzione — Rientrare nella sessione esistente:**
```bash
podman exec -it kali /bin/bash
```

**Soluzione — Se vuoi ricreare il container da zero:**
```bash
podman rm -f kali
podman run -it --name kali kalilinux/kali-rolling /bin/bash
```

---

### Errore: container in stato "exited"
**Messaggio:** `Error: container kali is not running`

**Soluzione:**
```bash
podman start kali
podman exec -it kali /bin/bash
```

---

### Errore: immagine non trovata localmente
**Messaggio:** `Error: image not known` o `unable to find image`

**Soluzione:**
```bash
podman pull kalilinux/kali-rolling
```

---

### Errore: conflitto di nome container
**Messaggio:** `Error: container name "kali" is already in use`

**Soluzione — Opzione 1: rimuovere il vecchio container:**
```bash
podman rm kali
podman run -it --name kali kalilinux/kali-rolling /bin/bash
```

**Soluzione — Opzione 2: usare un nome diverso:**
```bash
podman run -it --name kali2 kalilinux/kali-rolling /bin/bash
```

---

### Errore: permessi negati (rootless Podman)
**Messaggio:** `permission denied` o `cannot create /proc/...`

**Soluzione — Avviare con flag privileged:**
```bash
podman run -it --name kali --privileged kalilinux/kali-rolling /bin/bash
```

**Soluzione — Oppure eseguire Podman come root (sconsigliato in produzione):**
```bash
sudo podman run -it --name kali kalilinux/kali-rolling /bin/bash
```

---

### Errore: porta già in uso
**Messaggio:** `Error: address already in use` o `bind: address already in use`

**Soluzione — Trovare il processo che occupa la porta (es. 8080):**
```bash
lsof -i :8080
```

**Terminare il processo:**
```bash
kill -9 <PID>
```

**Oppure usare una porta diversa:**
```bash
podman run -it --name kali -p 9090:80 kalilinux/kali-rolling /bin/bash
```

---

### Errore: Podman non avviato / socket non disponibile
**Messaggio:** `Cannot connect to Podman. Did you start the service?`

**Soluzione — Avviare il servizio Podman (Linux):**
```bash
systemctl --user start podman.socket
```

**Su macOS con Podman Machine:**
```bash
podman machine start
```

**Verificare lo stato:**
```bash
podman machine list
```

---

### Errore: Podman Machine non inizializzata (macOS)
**Messaggio:** `Error: no such Podman machine`

**Soluzione:**
```bash
podman machine init
podman machine start
```

---

## 7. Comandi utili aggiuntivi

### Esportare il container come immagine (snapshot)
```bash
podman commit kali kali-custom
```

### Salvare l'immagine su file
```bash
podman save -o kali-custom.tar kali-custom
```

### Caricare un'immagine da file
```bash
podman load -i kali-custom.tar
```

### Rimuovere tutte le immagini non utilizzate
```bash
podman image prune -a
```

### Rimuovere tutti i container fermi
```bash
podman container prune
```
