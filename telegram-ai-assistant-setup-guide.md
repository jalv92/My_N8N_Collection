# 🤖 Guía de Configuración: Asistente AI de Telegram con Google Calendar y Gmail

## 📋 Descripción General

Este workflow crea un asistente AI inteligente en Telegram que puede:
- 📅 Gestionar tu calendario de Google (crear eventos, verificar disponibilidad, listar eventos)
- 📧 Enviar correos electrónicos a través de Gmail
- 🧠 Procesar comandos en lenguaje natural (español e inglés)
- 💾 Recordar contexto y preferencias del usuario

## 🚀 Requisitos Previos

### 1. Cuentas y Servicios Necesarios
- ✅ Cuenta de Telegram
- ✅ Cuenta de Google con acceso a Calendar y Gmail
- ✅ Cuenta de OpenAI (o servicio LLM alternativo)
- ✅ Instancia de n8n activa (versión 1.0+)
- ✅ Google Sheets (opcional, para memoria persistente)

### 2. Credenciales Requeridas
- 🔑 Token de Bot de Telegram
- 🔑 OAuth2 de Google (Calendar + Gmail)
- 🔑 API Key de OpenAI
- 🔑 Google Sheets API (opcional)

## 📝 Paso 1: Configurar Bot de Telegram

### Crear el Bot
1. Abre Telegram y busca **@BotFather**
2. Envía el comando `/newbot`
3. Elige un nombre para tu bot (ej: "Mi Asistente AI")
4. Elige un username único (debe terminar en "bot", ej: `mi_asistente_ai_bot`)
5. **Guarda el Token del Bot** (formato: `1234567890:ABCdefGHIjklMNOpqrSTUVwxyz`)

### Configurar el Bot
```
/setdescription - Asistente AI para gestionar calendario y emails
/setabouttext - Gestiono tu calendario de Google y envío emails por ti
/setcommands - Define los siguientes comandos:
  start - Iniciar conversación
  help - Mostrar ayuda
  calendario - Ver eventos de hoy
  disponibilidad - Verificar disponibilidad
```

## 🔐 Paso 2: Configurar Google OAuth2

### Crear Proyecto en Google Cloud Console
1. Ve a [Google Cloud Console](https://console.cloud.google.com)
2. Crea un nuevo proyecto o selecciona uno existente
3. Habilita las siguientes APIs:
   - Google Calendar API
   - Gmail API
   - Google Sheets API (opcional)

### Crear Credenciales OAuth2
1. Ve a **APIs & Services > Credentials**
2. Click en **Create Credentials > OAuth client ID**
3. Tipo de aplicación: **Web application**
4. Nombre: "n8n Integration"
5. **Authorized redirect URIs**: 
   ```
   https://tu-instancia-n8n.com/rest/oauth2-credential/callback
   ```
6. **Guarda el Client ID y Client Secret**

### Configurar Scopes
Asegúrate de autorizar los siguientes scopes:
```
https://www.googleapis.com/auth/calendar
https://www.googleapis.com/auth/calendar.events
https://www.googleapis.com/auth/gmail.send
https://www.googleapis.com/auth/gmail.compose
https://www.googleapis.com/auth/gmail.modify
https://www.googleapis.com/auth/spreadsheets (opcional)
```

## 🤖 Paso 3: Configurar OpenAI

1. Ve a [OpenAI Platform](https://platform.openai.com)
2. Crea una API Key en **API Keys**
3. **Guarda la API Key** de forma segura
4. Configura límites de uso si es necesario

## ⚙️ Paso 4: Configurar n8n

### Importar el Workflow

1. Abre tu instancia de n8n
2. Ve a **Workflows > Import**
3. Importa el archivo `telegram-ai-assistant-calendar-gmail.json`

### Configurar Credenciales en n8n

#### Telegram Bot
1. Ve a **Credentials > New**
2. Busca "Telegram"
3. Nombre: "Telegram Bot"
4. Access Token: `[Tu token del bot]`

#### Google OAuth2
1. Ve a **Credentials > New**
2. Busca "Google OAuth2 API"
3. Nombre: "Google OAuth2"
4. Client ID: `[Tu Client ID]`
5. Client Secret: `[Tu Client Secret]`
6. Autoriza y selecciona tu cuenta de Google

#### OpenAI
1. Ve a **Credentials > New**
2. Busca "OpenAI"
3. Nombre: "OpenAI API"
4. API Key: `[Tu API Key]`

## 🔧 Paso 5: Configurar el Workflow

### Actualizar el Webhook URL en Telegram

1. En n8n, abre el workflow
2. Click en el nodo "Telegram Trigger"
3. Copia la Webhook URL mostrada
4. Configura el webhook en Telegram usando curl:
```bash
curl -X POST "https://api.telegram.org/bot[TU_BOT_TOKEN]/setWebhook" \
     -H "Content-Type: application/json" \
     -d '{"url": "[WEBHOOK_URL_DE_N8N]"}'
```

### Personalizar el AI Agent

Edita el nodo "AI Agent - Assistant Brain" para personalizar:
- **Modelo**: gpt-4o, gpt-3.5-turbo, o tu preferencia
- **Temperature**: 0.3 (más bajo = más consistente)
- **Max Tokens**: 2000
- **System Prompt**: Personaliza las instrucciones del asistente

### Configurar Google Sheets para Memoria (Opcional)

1. Crea una hoja de cálculo en Google Sheets
2. Nombra la primera hoja como "UserMemory"
3. Crea las siguientes columnas:
   - A1: userId
   - B1: timestamp
   - C1: context
   - D1: preferences
4. Copia el ID de la hoja (está en la URL)
5. Actualiza los nodos "Memory - Store Context" y "Memory - Retrieve Context"

## 🎯 Paso 6: Probar el Workflow

### Pruebas Básicas

1. **Activar el workflow** en n8n
2. Envía un mensaje a tu bot en Telegram
3. Prueba los siguientes comandos:

```
"Hola, ¿qué puedes hacer?"
"¿Estoy libre mañana a las 3pm?"
"Programa una reunión con Juan el viernes a las 10am"
"Envía un correo a ejemplo@email.com sobre la reunión"
"¿Qué tengo agendado esta semana?"
```

### Verificar Logs

1. En n8n, ve a **Executions**
2. Revisa cada ejecución para verificar:
   - ✅ Mensaje recibido correctamente
   - ✅ AI Agent procesó el comando
   - ✅ Herramientas ejecutadas correctamente
   - ✅ Respuesta enviada a Telegram

## 🛠️ Solución de Problemas

### El bot no responde
- Verifica que el webhook esté configurado correctamente
- Revisa que el workflow esté activo
- Verifica los logs de ejecución en n8n

### Errores de autenticación con Google
- Re-autoriza las credenciales OAuth2
- Verifica que los scopes estén correctos
- Asegúrate de que las APIs estén habilitadas

### El AI no entiende los comandos
- Revisa el system prompt
- Ajusta la temperatura del modelo
- Proporciona ejemplos más claros en el prompt

### Errores de calendario
- Verifica permisos de la cuenta de Google
- Asegúrate de usar el calendario "primary"
- Revisa el formato de fechas y horas

## 📊 Comandos de Ejemplo

### Gestión de Calendario
```
"Crea un evento llamado 'Reunión de equipo' mañana a las 2pm"
"¿Estoy ocupado el lunes por la mañana?"
"Cancela mi reunión del jueves"
"Muéstrame mi agenda de la próxima semana"
"Agrega una cita con el doctor el 15 de enero a las 4pm"
```

### Envío de Emails
```
"Envía un correo a juan@ejemplo.com diciendo que llegaré 10 minutos tarde"
"Crea un borrador de email para María sobre el proyecto X"
"Envía un recordatorio por email a todo el equipo sobre la reunión"
```

### Comandos Combinados
```
"Programa una reunión con Carlos el martes a las 3pm y envíale un email con los detalles"
"Verifica si estoy libre el viernes y si sí, agenda una llamada con el cliente y notifícale por correo"
```

## 🔒 Consideraciones de Seguridad

1. **Nunca compartas** tokens o API keys públicamente
2. **Limita los permisos** de OAuth2 solo a lo necesario
3. **Configura rate limiting** en OpenAI si es necesario
4. **Revisa regularmente** los logs de ejecución
5. **Implementa validación** de usuarios si el bot es público

## 📈 Mejoras Opcionales

### Añadir más funcionalidades
- Integración con Slack o Microsoft Teams
- Soporte para adjuntos en emails
- Recordatorios automáticos
- Integración con CRM
- Análisis de sentimiento en mensajes

### Optimizaciones
- Implementar caché para consultas frecuentes
- Usar modelos LLM locales para mayor privacidad
- Añadir autenticación de usuarios
- Implementar logs detallados

## 📚 Recursos Adicionales

- [Documentación de n8n](https://docs.n8n.io)
- [API de Telegram Bot](https://core.telegram.org/bots/api)
- [Google Calendar API](https://developers.google.com/calendar/api/v3/reference)
- [Gmail API](https://developers.google.com/gmail/api/reference/rest)
- [OpenAI API Reference](https://platform.openai.com/docs/api-reference)

## 🤝 Soporte

Si encuentras problemas o tienes preguntas:
1. Revisa los logs de ejecución en n8n
2. Consulta la documentación oficial de cada servicio
3. Verifica que todas las credenciales estén configuradas correctamente
4. Asegúrate de que el workflow esté activo

---

**Versión**: 1.0.0  
**Última actualización**: Enero 2025  
**Autor**: AI Assistant Development Team  
**Licencia**: MIT