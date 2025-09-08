# infra-n8n

Automatización con **n8n** auto-hospedado usando **Docker Compose** y **PostgreSQL (pgvector)**.  
Incluye scripts de inicialización de base de datos y una estructura mínima para levantar el stack rápidamente en entornos locales o servidores.

---

## 🚀 Quick start

1) **Clonar y entrar al proyecto**
```bash
git clone https://github.com/sisantacruzm/infra-n8n.git
cd infra-n8n
```

2) **Crear variables de entorno**
```bash
cp .env.example .env
# Edita .env con tus valores
```

3) **Levantar servicios**
```bash
docker compose up -d
```

4) **Acceso**
- **n8n (UI)**: `http://localhost:15010/`
- **PostgreSQL**: `localhost:15009`

El callback OAuth por defecto de n8n suele ser:  
`http://<host>:<puerto>/rest/oauth2-credential/callback`  

---

## 📦 ¿Qué incluye?

- **n8n**: plataforma de automatización de flujos.
- **PostgreSQL con pgvector**: base de datos transaccional + embeddings/vectoriales.
- **init-scripts/**: scripts para inicializar/configurar la BD en el primer arranque.
- **Healthchecks** y **volúmenes** persistentes para los datos.

---

## ⚙️ Configuración

### Variables principales (editar en `.env`)
```env
TZ=America/Bogota
POSTGRES_DB=n8n
POSTGRES_USER=n8n
POSTGRES_PASSWORD=cambia_esto
DATA_PATH=./data
PG_HOST_PORT=15009
N8N_PORT=15010
N8N_PROTOCOL=http
N8N_HOST=localhost
WEBHOOK_URL=https://tudominio.com/
```

---

## 🧱 Servicios (resumen)

- **PostgreSQL (pgvector)**
  - Puerto host: **15009** → contenedor **5432**
  - Datos persistentes: `${DATA_PATH}/postgres:/var/lib/postgresql/data`
  - Healthcheck con `pg_isready`

- **n8n**
  - Puerto host: **15010** (UI web)
  - Depende de Postgres
  - Variables `N8N_*` y `WEBHOOK_URL` para URLs externas / OAuth

---

## 🔐 OAuth & dominios

Si vas a conectar Google, Slack, GitHub u otros que usan OAuth:

1. Configura URL pública en `.env`.
2. Registra el callback en el proveedor OAuth:
   - `https://tudominio.com/rest/oauth2-credential/callback`
3. Si trabajas en localhost y no lo permiten:
   - Usa túnel temporal (ngrok) o subdominio público.

---

## 🧰 Comandos útiles

```bash
docker compose logs -f
docker compose restart n8n
docker compose pull && docker compose up -d
```

---

## 💾 Backups & restore (PostgreSQL)

```bash
# Backup
docker compose exec -T postgres pg_dump -U "$POSTGRES_USER" -d "$POSTGRES_DB" > backup.sql

# Restore
docker compose exec -T postgres psql -U "$POSTGRES_USER" -d "$POSTGRES_DB" < backup.sql
```

---

## 🛡️ Producción (buenas prácticas)

- Reverse proxy con TLS
- Restringir acceso a UI
- Backups automáticos
- Monitoreo
- Actualizaciones periódicas

---

## 🐞 Troubleshooting

- **OAuth redirige a localhost** → revisa `N8N_HOST`, `N8N_PROTOCOL`, `WEBHOOK_URL`.
- **No conecta a Postgres** → comprueba variables y puertos.
- **Errores con Telegram / Gmail / Sheets** → valida callback y dominio.

---

## 📂 Estructura del repo

```
infra-n8n/
├─ compose.yml
├─ .env.example
└─ init-scripts/
```

---

## 📜 Licencia

Licencia MIT.

---

## 🤝 Contribuciones

Issues y PRs son bienvenidos.
