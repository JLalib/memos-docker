# 📝 Memos Docker - Aplicación de Notas Rápidas y Privadas

**Memos Docker** es la implementación en contenedores de **Memos**, una aplicación minimalista de notas diseñada para capturar ideas, snippets y pensamientos al instante sin la fricción de organizar carpetas o títulos. Basada en un concepto de "timeline", Memos permite un flujo de trabajo de *captura ahora, organiza después* utilizando Markdown y etiquetas.

---

## ✨ Características principales

- **Markdown-Native**: Soporte completo para Markdown, permitiendo notas ricas, legibles y portables.
- **Flujo sin Fricción**: No requiere títulos ni estructuras previas. Escribe y guarda inmediatamente.
- **Timeline Visual**: Visualización cronológica de todas tus notas, similar a un muro de red social privado.
- **Organización mediante Tags**: Uso de `#etiquetas` para categorizar contenido y filtrar rápidamente.
- **Tareas Integradas**: Soporte para checkboxes (`- [ ]`) para gestionar pendientes dentro de las notas.
- **Gestión de Multimedia**: Soporte para pegar imágenes directamente desde el portapapeles.
- **API REST y gRPC**: Altamente extensible para integraciones con otros servicios o automatizaciones.
- **Privacidad Total**: Self-hosted, sin telemetría y con control absoluto sobre la base de datos.
- **Ligero y Rápido**: Escrito en Go y React, con un consumo de recursos extremadamente bajo.

---

## ⚠️ Requisitos previos

- **Docker** y **Docker Compose** (versión 2.x o superior).
- **256 MB - 512 MB RAM** (Muy ligero).
- **500 MB - 2 GB espacio en disco**.
- **Puerto 5230** disponible (configurable).
- **Base de datos**: SQLite (incluido por defecto) o PostgreSQL (recomendado para producción).

---

## ⚙️ Configuración con Docker Compose

### 1. Clona este repositorio y accede al directorio:
```bash
git clone https://github.com/JLalib/memos-docker.git
cd memos-docker
```

### 2. Archivo `docker-compose.yml` (Versión Profesional con PostgreSQL)
Crea el archivo `docker-compose.yml` con la siguiente configuración para asegurar la robustez de los datos:

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:15-alpine
    container_name: memos-postgres
    restart: unless-stopped
    environment:
      POSTGRES_DB: memos
      POSTGRES_USER: memos
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    volumes:
      - postgres_data:/var/lib/postgresql/data

  memos:
    image: neosmemo/memos:stable
    container_name: memos
    restart: unless-stopped
    ports:
      - "5230:5230"
    environment:
      - MEMOS_DRIVER=postgres
      - MEMOS_DSN=postgres://memos:${DB_PASSWORD}@postgres:5432/memos?sslmode=disable
    volumes:
      - ./memos_data:/var/opt/memos
    depends_on:
      - postgres

volumes:
  postgres_data:
```

### 3. Configura las variables de entorno
Crea un archivo `.env` en la raíz:
```env
DB_PASSWORD=tu_contrasena_segura_aqui
```

### 4. Despliega la aplicación
```bash
docker compose up -d
```

### 5. Accede a la interfaz
Abre en tu navegador:
🔗 [http://localhost:5230](http://localhost:5230)

---

## 🛠️ Primeros pasos

### 1. Registro de cuenta
- Al acceder por primera vez, haz clic en **"Sign up"**.
- Crea tu usuario y contraseña. El primer usuario registrado tiene control total sobre la instancia.

### 2. Tu primer Memo
- Escribe cualquier idea en el campo superior.
- Usa Markdown para dar formato: `**negrita**`, `*cursiva*` o bloques de \`código\`.
- Presiona `Ctrl + Enter` para publicar.

### 3. Organización Eficiente
- Añade `#tags` en el texto de tu nota.
- Haz clic en cualquier tag para filtrar el timeline y ver solo las notas relacionadas.

### 4. Gestión de Tareas
- Escribe `- [ ] Mi tarea` para crear una lista de verificación interactiva.

---

## 🔒 Seguridad y recomendaciones

### 1. Gestión de Datos
Si utilizas la versión de SQLite (simplificada), recuerda que toda la base de datos es un solo archivo. Realiza copias frecuentes de la carpeta `./data`.

### 2. Acceso Remoto Seguro (HTTPS)
Para exponer Memos a internet, utiliza **Caddy** como reverse proxy:

**`Caddyfile`**:
```
memos.tudominio.com {
    reverse_proxy localhost:5230
}
```

### 3. Backups de Producción (PostgreSQL)
Para respaldar tu base de datos de manera profesional:
```bash
docker exec memos-postgres pg_dump -U memos memos > memos-backup-$(date +%Y%m%d).sql
```

---

## 📂 Estructura del proyecto

```
./
├── docker-compose.yml    # Orquestación de Memos y PostgreSQL
├── .env                  # Variables de entorno (contraseñas)
├── memos_data/           # Almacenamiento de archivos y adjuntos
└── README-Memos-Docker.md  # Este archivo
```

---

## 🔄 Actualización y mantenimiento

### Actualizar Memos
```bash
docker compose pull
docker compose up -d
```

### Comandos útiles
| Comando                          | Descripción                                  |
|----------------------------------|----------------------------------------------|
| `docker logs -f memos`           | Ver logs de la aplicación en tiempo real.     |
| `docker compose restart`         | Reiniciar los servicios.                       |
| `docker compose ps`              | Verificar estado de los contenedores.          |

---

## 📊 Comparativa con alternativas

| Característica               | Memos Docker      | Notion            | Google Keep       | Obsidian          |
|------------------------------|-------------------|-------------------|-------------------|-------------------|
| **Autohospedado**            | ✅ Sí             | ❌ No             | ❌ No             | ✅ Sí (Local)     |
| **Privacidad / Zero Tracking**| ✅ Total          | ❌ Limitado       | ❌ Nulo           | ✅ Total           |
| **Sencillez de Captura**     | ✅ Máxima         | ⚠️ Media          | ✅ Alta           | ⚠️ Media          |
| **Timeline Visual**          | ✅ Sí             | ❌ No             | ❌ No             | ❌ No             |
| **Recursos (RAM/CPU)**       | ✅ Ultraligero    | ❌ Pesado (SaaS)  | ✅ Ligero         | ✅ Ligero         |

---

## 📚 Referencias

- [Memos Official Website](https://www.usememos.com/)
- [GitHub Repository](https://github.com/usememos/memos)
- [Official Documentation](https://www.usememos.com/docs)
- [Docker Hub Image](https://hub.docker.com/r/neosmemo/memos)

---
