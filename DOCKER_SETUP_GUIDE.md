# Guida Setup Docker per LibreChat

## Stato Attuale

### ✅ Completato
1. **Installazione Docker e Docker Compose**
   - Docker 28.2.2 installato
   - Docker Compose 1.29.2 installato
   - Daemon Docker configurato per funzionare in ambiente containerizzato

2. **Configurazione Ambiente**
   - File `.env` creato con configurazioni base
   - UID e GID configurati (0:0 per ambiente root)
   - Porta configurata: 3080 (backend) e 3090 (frontend dev)

3. **Dipendenze Node.js**
   - Node.js v22.21.1 installato
   - Npm 10.9.4 installato
   - Tutti i pacchetti npm installati con successo (3631 packages)

4. **Build Pacchetti**
   - `@librechat/data-provider` compilato
   - `@librechat/data-schemas` compilato
   - `@librechat/api` compilato
   - `@librechat/client` compilato

5. **Frontend Development Server**
   - ✅ **Frontend avviato e funzionante**
   - URL: **http://localhost:3090/**
   - Vite Development Server in esecuzione

### ⚠️ Limitazioni Ambiente

L'ambiente ha le seguenti restrizioni di rete che impediscono il setup completo con Docker:
- Impossibile scaricare immagini da Docker Hub (errore 403 Forbidden)
- Impossibile accedere a repository MongoDB ufficiali
- Proxy di rete con restrizioni

### 🔧 Setup Completo con Docker (Per Ambienti Senza Restrizioni)

Per completare il setup in un ambiente con accesso completo a Internet:

#### 1. Verificare Docker
```bash
docker --version
docker-compose --version
```

#### 2. Avviare i Container
```bash
cd /home/user/LibreChat
docker-compose up -d
```

Questo avvierà i seguenti servizi:
- **LibreChat API** (porta 3080)
- **MongoDB** (database)
- **MeiliSearch** (ricerca)
- **PostgreSQL** con pgvector (RAG)
- **RAG API** (porta 8000)

#### 3. Verificare lo Stato
```bash
docker-compose ps
```

#### 4. Accedere all'Interfaccia
Una volta che tutti i container sono avviati, l'interfaccia sarà disponibile su:
```
http://localhost:3080
```

#### 5. Creare il Primo Utente
```bash
npm run create-user
```

### 📦 Immagini Docker Richieste

Il file `docker-compose.yml` configura le seguenti immagini:
- `ghcr.io/danny-avila/librechat-dev:latest` - API principale
- `mongo:latest` - Database MongoDB
- `getmeili/meilisearch:v1.12.3` - Motore di ricerca
- `pgvector/pgvector:0.8.0-pg15-trixie` - Database vettoriale
- `ghcr.io/danny-avila/librechat-rag-api-dev-lite:latest` - API RAG

### 🛠️ Setup Development Locale (Alternativa)

Se Docker non è disponibile, puoi avviare LibreChat in modalità development:

#### 1. Installare MongoDB Localmente
```bash
# Vedere documentazione MongoDB per il tuo OS
# https://docs.mongodb.com/manual/installation/
```

#### 2. Installare MeiliSearch (Opzionale)
```bash
# Vedere https://www.meilisearch.com/docs/learn/getting_started/installation
```

#### 3. Avviare Backend
```bash
npm run backend:dev
```

#### 4. Avviare Frontend (in altra finestra)
```bash
npm run frontend:dev
```

L'interfaccia sarà disponibile su:
```
Frontend: http://localhost:3090/
Backend API: http://localhost:3080/api
```

### 📝 Configurazione `.env`

Il file `.env` contiene le seguenti configurazioni importanti:
```bash
HOST=localhost
PORT=3080
MONGO_URI=mongodb://127.0.0.1:27017/LibreChat
DOMAIN_CLIENT=http://localhost:3080
DOMAIN_SERVER=http://localhost:3080
UID=0
GID=0
MEILI_HOST=http://0.0.0.0:7700
MEILI_MASTER_KEY=DrhYf7zENyR6AlUCKmnz0eYASOQdl6zxH7s7MKFSfFCt
```

### 🚀 Script NPM Disponibili

```bash
# Backend
npm run backend          # Avvia backend in production
npm run backend:dev      # Avvia backend in development mode
npm run backend:stop     # Ferma il backend

# Frontend
npm run frontend:dev     # Avvia frontend in development mode
npm run build:client     # Build frontend per production

# Utilità
npm run create-user      # Crea nuovo utente
npm run list-users       # Lista utenti esistenti
npm run reset-password   # Reset password utente
npm run ban-user         # Ban utente
npm run delete-user      # Elimina utente

# Build
npm run build:packages   # Build tutti i pacchetti interni
```

### 🔍 Troubleshooting

#### Errore: "Cannot connect to the Docker daemon"
```bash
# Verificare che Docker sia in esecuzione
dockerd > /tmp/dockerd.log 2>&1 &
sleep 5
docker ps
```

#### Errore: Porta già in uso
```bash
# Modificare la porta nel file .env
PORT=8080
```

#### Frontend non si connette al Backend
Verificare che il backend sia avviato e accessibile su `http://localhost:3080/api`

### 📚 Risorse Utili

- Documentazione Ufficiale: https://docs.librechat.ai
- Repository GitHub: https://github.com/danny-avila/LibreChat
- Discord Community: https://discord.librechat.ai

### ✅ URL di Accesso

#### Ambiente Corrente (Development)
- **Frontend**: http://localhost:3090/ (✅ Attivo)
- **Backend**: http://localhost:3080/ (richiede MongoDB)

#### Con Docker (Production)
- **Interfaccia Completa**: http://localhost:3080/

---

## Note Tecniche

### Configurazione Docker Daemon
Per l'ambiente containerizzato, Docker è stato configurato con:
```json
{
  "iptables": false,
  "ip6tables": false,
  "storage-driver": "vfs"
}
```

Questo permette a Docker di funzionare in ambienti senza supporto completo per iptables o overlay filesystem.

### Build dei Pacchetti Interni
LibreChat usa un'architettura monorepo con workspaces npm. I pacchetti interni devono essere compilati prima di avviare il frontend:
- `packages/data-provider` - Provider per gestione dati
- `packages/data-schemas` - Schemi e validazioni
- `packages/api` - Logica API condivisa
- `packages/client` - Componenti UI condivisi

Questo è già stato fatto e i pacchetti sono pronti all'uso.
