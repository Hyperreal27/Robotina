# Robotina - Flujos de Automatización n8n

Sistema de automatización para gestión de servicios de limpieza con WhatsApp, IA y base de datos.

## 📁 Flujos Incluidos

### 1. **Robotina: Extracción de mensajes + Consultas**

Chatbot inteligente que captura mensajes de WhatsApp y responde automáticamente.

**¿Qué hace?**
- Recibe mensajes en tiempo real a través de WhatsApp (Evolution API)
- Extrae y valida la información del mensaje
- Almacena todos los mensajes en PostgreSQL (`raw_messages`)
- Si menciona "Robotina", activa inteligencia artificial
- La IA puede consultar:
  - Asignaciones de limpieza
  - Incidentes diarios
  - Información de limpiadores
  - Datos de propiedades
  - Sistema de tracking
  - Códigos e instrucciones
- Recuerda conversaciones anteriores (memoria en PostgreSQL)
- Busca información relacionada (Qdrant Vector Store)
- Responde por WhatsApp

**Flujo:**
```
Webhook WhatsApp 
    ↓
¿Es mensaje nuevo?
    ↓
Extraer Datos
    ↓
¿Es Grupo?
    ↓
Guardar en BD
    ↓
¿Menciona Robotina?
    ↓
IA (OpenAI) → Responde
    ↓
Enviar por WhatsApp
```

**Requisitos:**
- Cuenta en Evolution API (WhatsApp)
- API Key de OpenAI
- Base de datos PostgreSQL
- Qdrant para búsqueda vectorial

---

### 2. **Robotina: Resúmenes diarios**

Procesa mensajes del día y genera reportes automáticos.

**¿Qué hace?**
- Se ejecuta automáticamente cada día a las **21:00 (9 PM)**
- Extrae todos los mensajes del día desde la BD
- Consolida y formatea los mensajes
- Usa IA (Google Gemini) para analizar:
  - Busca información de limpiadores
  - Busca aliases de limpiadores
  - Busca información de propiedades
  - Busca aliases de propiedades
- Extrae automáticamente las asignaciones de servicios
- Guarda en tabla `cleaning_assignments`
- Prepara resumen formateado
- Envía el resumen al grupo de WhatsApp
- Almacena en Qdrant para búsquedas futuras

**Flujo:**
```
Cron 21:00 (9 PM)
    ↓
Select mensajes del día
    ↓
Consolidar Mensajes
    ↓
¿Hay mensajes?
    ↓
IA (Google Gemini) → Analiza
    ↓
Parsear Output
    ↓
├─ Separar Asignaciones
│  └─ Guardar en BD
└─ Preparar Resumen
    └─ Enviar por WhatsApp
         ↓
    Guardar en Qdrant
```

**Requisitos:**
- Google Gemini API
- Base de datos PostgreSQL
- Qdrant para búsqueda vectorial
- Cuenta en Evolution API (WhatsApp)

---

## 🗄️ Base de Datos

### Tablas principales:

**raw_messages** - Almacena todos los mensajes
```
- fecha_envio
- hora_envio
- mensaje_texto
- sender_name
- sender_number
- remote_jid
- message_id
- tipo_mensaje
- fecha_servicio
```

**cleaning_assignments** - Almacena asignaciones de servicios
```
- fecha_servicio
- propiedad
- limpiador
- tipo_servicio
- notas
- created_at
```

---

## 🔌 Integraciones

- **WhatsApp**: Evolution API
- **IA**: OpenAI (GPT) + Google Gemini
- **Base de datos**: PostgreSQL
- **Búsqueda semántica**: Qdrant Vector Store
- **n8n**: Orquestación de flujos

---

## 🚀 Cómo usar

1. **Descargar el flujo:**
   - Abre el archivo `.json` con el flujo

2. **Importar en n8n:**
   - Ve a tu instancia de n8n
   - Haz clic en "Import"
   - Selecciona el archivo `.json`

3. **Configurar credenciales:**
   - Evolution API (WhatsApp)
   - OpenAI / Google Gemini
   - PostgreSQL
   - Qdrant

4. **Activar:**
   - Haz clic en "Activate" para activar el flujo

---

## ⚙️ Configuración necesaria

**Flujo 1 (Mensajes + Consultas):**
- Webhook path: `robotina`
- Tabla PostgreSQL: `raw_messages`
- Conexión OpenAI: API Key

**Flujo 2 (Resúmenes):**
- Cron: `0 21 * * *` (9 PM diarios)
- Tabla PostgreSQL: `raw_messages`, `cleaning_assignments`
- Conexión Google Gemini: API Key
- Grupo WhatsApp: `120363418355921872@g.us`

---

## 📝 Notas

- Los flujos requieren que la BD PostgreSQL esté configurada
- Las credenciales de APIs externas deben estar en n8n
- El webhook de WhatsApp debe estar configurado en Evolution API
- Qdrant debe estar ejecutándose para la búsqueda vectorial

---

**Última actualización:** Marzo 2026
