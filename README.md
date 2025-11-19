
# WhatsApp Appointment Agent

Agente conversacional inteligente para WhatsApp que gestiona citas automáticamente usando n8n, Evolution API y OpenAI.

## 📋 Descripción

Sistema completo de automatización de WhatsApp que permite a los clientes reservar, consultar y gestionar citas mediante conversaciones naturales. El agente procesa mensajes de texto y audio, mantiene contexto conversacional, valida disponibilidad de horarios y registra automáticamente nuevos leads.

## ✨ Características Principales

- **Conversación Natural**: Interacción fluida usando GPT-4 para comprensión de intenciones
- **Gestión Completa de Citas**: Reserva, consulta, modificación y cancelación de citas
- **Memoria Contextual**: Mantiene el historial de conversación con Redis
- **Búsqueda Semántica**: Utiliza Qdrant para respuestas basadas en conocimiento del negocio
- **Procesamiento de Audio**: Transcripción automática de notas de voz con Whisper
- **Validación de Horarios**: Respeta disponibilidad y horarios de negocio
- **Sistema de Recordatorios**: Envío automático de confirmaciones y recordatorios
- **Registro de Leads**: Captura y almacenamiento automático de datos de clientes

## 🏗️ Arquitectura del Sistema

```
Cliente WhatsApp
    ↓
Evolution API (Gateway WhatsApp)
    ↓
Webhook n8n
    ↓
Cola de Mensajes (Redis)
    ↓
├── Recuperación de Contexto (Redis)
├── Búsqueda en Base de Conocimiento (Qdrant)
├── Procesamiento IA (OpenAI GPT-4)
├── Transcripción de Audio (Whisper)
└── Gestión de Citas
    ↓
Respuesta al Cliente
```

## 🛠️ Stack Tecnológico

- **n8n**: Orquestación de workflows
- **Evolution API**: Integración con WhatsApp
- **OpenAI GPT-4**: Procesamiento de lenguaje natural
- **Redis**: Gestión de sesiones y colas
- **Qdrant**: Base de datos vectorial para búsqueda semántica
- **Docker**: Contenedorización de servicios

## 📦 Requisitos Previos

- Docker y Docker Compose instalados
- Node.js 18+ (para n8n)
- Cuenta de OpenAI con créditos disponibles
- Instancia de Evolution API configurada
- n8n (self-hosted o cloud)
- Mínimo 2GB RAM disponible

## 🚀 Instalación

### Paso 1: Clonar el repositorio

```bash
git clone https://github.com/jlzapatafernandez65-glitch/Whatsapp-Agent-N8N.git
cd Whatsapp-Agent-N8N
```

### Paso 2: Configurar variables de entorno

```bash
cp .env.example .env
```

Edita el archivo `.env` con tus credenciales:

```env
# Evolution API
EVOLUTION_API_URL=https://tu-dominio-evolution.com
EVOLUTION_API_KEY=tu-api-key
EVOLUTION_INSTANCE_NAME=nombre-instancia

# OpenAI
OPENAI_API_KEY=sk-tu-api-key-openai

# Redis
REDIS_HOST=localhost
REDIS_PORT=6379
REDIS_PASSWORD=tu-password-seguro

# Qdrant
QDRANT_URL=http://localhost:6333
QDRANT_API_KEY=tu-api-key-qdrant

# Configuración de Negocio
BUSINESS_NAME=Tu Negocio
BUSINESS_TIMEZONE=Europe/Madrid
BUSINESS_HOURS_START=09:00
BUSINESS_HOURS_END=19:00
```

### Paso 3: Iniciar servicios con Docker

```bash
docker-compose up -d
```

### Paso 4: Importar workflow en n8n

1. Accede a tu instancia de n8n: `http://localhost:5678`
2. Ve a **Workflows** → **Import from File**
3. Selecciona el archivo `workflows/whatsapp-appointment-agent.json`
4. Configura las credenciales necesarias:
   - OpenAI API Key
   - Evolution API credentials
   - Redis connection
   - Qdrant API Key
5. Activa el workflow

### Paso 5: Configurar webhook en Evolution API

En tu instancia de Evolution API, configura el webhook apuntando a:

```
https://tu-dominio-n8n.com/webhook/whatsapp-agent
```

## 📁 Estructura del Proyecto

```
Whatsapp-Agent-N8N/
├── workflows/
│   ├── whatsapp-appointment-agent.json    # Workflow principal
│   └── appointment-reminder.json          # Recordatorios automáticos
├── docs/
│   ├── INSTALLATION.md                    # Guía de instalación detallada
│   ├── CONFIGURATION.md                   # Configuración avanzada
│   ├── TECHNICAL.md                       # Documentación técnica
│   └── TROUBLESHOOTING.md                 # Solución de problemas
├── scripts/
│   ├── setup-qdrant.sh                    # Script de inicialización Qdrant
│   └── test-webhook.sh                    # Prueba de webhook
├── screenshots/                            # Capturas de ejemplo
├── docker-compose.yml                      # Configuración Docker
├── .env.example                            # Plantilla de variables
├── .gitignore
├── LICENSE
└── README.md
```

## 💬 Ejemplo de Uso

**Conversación típica de reserva de cita:**

```
Cliente: Hola, necesito una cita para corte de cabello

Bot: ¡Hola! Claro, con gusto te ayudo. ¿Qué día prefieres para tu corte?

Cliente: El viernes

Bot: Perfecto. El viernes 22 de noviembre tengo estos horarios disponibles:
     • 10:00 AM
     • 15:30 PM
     • 17:00 PM
     ¿Cuál te viene mejor?

Cliente: 15:30 está bien

Bot: Excelente. Necesito confirmar algunos datos:
     📅 Fecha: Viernes 22 de noviembre
     ⏰ Hora: 15:30
     ✂️ Servicio: Corte de cabello
     ⏱️ Duración: 30 minutos
     
     Por favor confirma con tu nombre completo.

Cliente: Juan Pérez

Bot: ¡Perfecto Juan! Tu cita ha sido confirmada ✅
     
     Te enviaré un recordatorio 24 horas antes.
     Si necesitas cancelar o modificar, simplemente escríbeme.
     
     ¡Nos vemos el viernes!
```

## 🔧 Configuración Avanzada

### Personalización del Comportamiento del Agente

El comportamiento del agente se puede personalizar editando el prompt en el nodo "AI Agent" del workflow:

```javascript
// Personaliza el tono y estilo
BUSINESS_TONE: "profesional y amigable"
BUSINESS_STYLE: "formal/informal/casual"

// Define servicios disponibles
SERVICES: ["corte", "tinte", "peinado", "tratamiento"]

// Configura duración de servicios
SERVICE_DURATION: {
  "corte": 30,
  "tinte": 90,
  "peinado": 45
}
```

### Configuración de Horarios

Edita los horarios de disponibilidad en el archivo `.env`:

```env
# Horarios por día de la semana
MONDAY_START=09:00
MONDAY_END=19:00
TUESDAY_START=09:00
TUESDAY_END=19:00
# ... resto de días

# Días cerrados
CLOSED_DAYS=["domingo"]
```

## 🐛 Troubleshooting

### El bot no responde

**Verificar:**
1. Evolution API está conectada: `curl http://evolution-api/status`
2. n8n está ejecutándose: `docker ps | grep n8n`
3. Webhook está configurado correctamente en Evolution API
4. El workflow está activado en n8n

**Logs:**
```bash
docker-compose logs -f n8n
docker-compose logs -f redis
```

### Error de conexión con Redis

```bash
# Reiniciar servicio Redis
docker-compose restart redis

# Verificar logs
docker-compose logs redis
```

### Respuestas lentas o timeout

**Optimizaciones:**
- Aumentar `max_tokens` en OpenAI (evitar respuestas cortadas)
- Configurar timeout más alto en webhook (60s recomendado)
- Revisar uso de memoria de Redis
- Optimizar tamaño de colección en Qdrant

### Webhook no recibe mensajes

**Verificar:**
1. URL del webhook es accesible públicamente
2. Certificado SSL válido (si usas HTTPS)
3. Firewall no bloquea puerto del webhook
4. Evolution API tiene el webhook correctamente configurado

## 📊 Casos de Uso

- **Salones de Belleza**: Reserva de cortes, tintes, manicura, peinados
- **Clínicas y Consultorios**: Gestión de citas médicas y seguimiento
- **Consultoría**: Agendamiento de sesiones y reuniones
- **Servicios Profesionales**: Abogados, contadores, terapeutas
- **Fitness & Wellness**: Clases personales, nutrición, yoga, masajes
- **Talleres Mecánicos**: Revisiones, reparaciones, mantenimiento
- **Centros Educativos**: Tutorías, asesorías, clases particulares

## 🔒 Seguridad

- Todas las credenciales se almacenan en variables de entorno
- Comunicación HTTPS con Evolution API
- Tokens de autenticación para webhooks de n8n
- Validación de mensajes entrantes
- Rate limiting implementado en Redis
- Logs de seguridad activados

## 📈 Roadmap

- [ ] Integración con Google Calendar
- [ ] Soporte para pagos integrados (Stripe/PayPal)
- [ ] Dashboard web de administración
- [ ] Análisis de sentimientos en conversaciones
- [ ] Soporte para múltiples agentes simultáneos
- [ ] Recordatorios automáticos personalizables
- [ ] Exportación de reportes de citas
- [ ] API REST para integración externa

## 🤝 Contribuir

Las contribuciones son bienvenidas. Para contribuir:

1. Fork el proyecto
2. Crea una rama para tu feature: `git checkout -b feature/NuevaFuncionalidad`
3. Commit tus cambios: `git commit -m 'Añade nueva funcionalidad'`
4. Push a la rama: `git push origin feature/NuevaFuncionalidad`
5. Abre un Pull Request

## 📝 Licencia

Este proyecto está bajo la Licencia MIT. Ver el archivo [LICENSE](LICENSE) para más detalles.

## 👨‍💻 Autor

**José Luis Zapata**
- 📍 Dos Hermanas, Sevilla, España
- 💼 AI Automation Specialist
- 🏢 José Luis Zapata IA - Consultancy
- 🔗 GitHub: [@jlzapatafernandez65-glitch](https://github.com/jlzapatafernandez65-glitch)

## 🙏 Agradecimientos

- [n8n.io](https://n8n.io) - Plataforma de automatización de workflows
- [Evolution API](https://evolution-api.com) - Gateway de WhatsApp
- [OpenAI](https://openai.com) - Modelos de IA (GPT-4, Whisper)
- [Qdrant](https://qdrant.tech) - Base de datos vectorial
- [Redis](https://redis.io) - Sistema de caché y colas

## 📞 Soporte

¿Necesitas ayuda o tienes preguntas?

- 📧 Abre un [Issue](https://github.com/jlzapatafernandez65-glitch/Whatsapp-Agent-N8N/issues)
- 📚 Consulta la [documentación completa](./docs/)
- 💬 Contacta directamente para implementaciones personalizadas

---

⭐ **Si este proyecto te resulta útil, dale una estrella en GitHub** ⭐

**Desarrollado con ❤️ en Sevilla, España**
