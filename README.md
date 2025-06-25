# 🚀 Asistente IA Multi-Herramienta para WhatsApp con n8n 🚀

Este proyecto transforma tu n8n en un poderoso Asistente de Inteligencia Artificial accesible a través de WhatsApp. Impulsado por Google Gemini, este bot no solo conversa, sino que actúa. Puede gestionar tu agenda, enviar correos electrónicos y recordar tus conversaciones para ofrecer una experiencia fluida y personalizada.

Es la solución perfecta para automatizar tareas diarias, gestionar comunicaciones y tener un verdadero copiloto digital en tu bolsillo.

## ✨ Capacidades del Asistente

Este sistema se compone de tres flujos de trabajo interconectados que le otorgan las siguientes habilidades:

- 🤖 **Agente Conversacional Avanzado:** Utiliza **Google Gemini** para entender y responder a las solicitudes en lenguaje natural a través de WhatsApp.
- 🗓️ **Gestión Completa de Google Calendar:**
  - Agendar nuevos eventos y reuniones.
  - Consultar eventos existentes.
  - Actualizar y eliminar eventos de tu calendario.
  - Comprobar la disponibilidad en tu agenda.
- 📧 **Envío de Correos Electrónicos:** Redacta y envía emails directamente desde la conversación de WhatsApp a través de tu cuenta de **Gmail**.
- 🧠 **Memoria Persistente:** Gracias a la integración con **PostgreSQL** y **Redis**, el asistente recuerda el contexto de conversaciones anteriores, permitiendo un seguimiento coherente y natural.
- 📱 **Integración Nativa con WhatsApp:** Se conecta a tu línea de WhatsApp utilizando **Evolution API**, permitiendo una interacción directa y sin intermediarios.

## 📋 Requisitos Previos

Para que el asistente funcione, necesitas tener acceso a las siguientes plataformas y servicios:

1.  Una instancia de **n8n** auto-hospedada o en la nube.
2.  Una cuenta de **Google Cloud Platform** para crear credenciales.
3.  Una base de datos **PostgreSQL**.
4.  Un servidor **Redis**.
5.  Una instancia de **Evolution API** conectada a un número de WhatsApp.

## 🛠️ Guía de Configuración de Credenciales

Este es el paso más importante. Debes crear una credencial en n8n para cada servicio.

### 1. Credencial de Google (para Gemini, Calendar y Gmail)

Crearemos una única credencial de Google OAuth 2.0 que dará acceso a los tres servicios.

**Paso 1: Configurar el Proyecto en Google Cloud**

1.  Ve a la [Consola de Google Cloud](https://console.cloud.google.com/).
2.  Crea un nuevo proyecto (ej. "Asistente-n8n").
3.  Dentro de tu proyecto, ve a **APIs y Servicios -> Biblioteca**.
4.  Busca y activa las siguientes APIs, una por una:
    - `Google Calendar API`
    - `Gmail API`
    - `Vertex AI API` (para Google Gemini)

**Paso 2: Configurar la Pantalla de Consentimiento OAuth**

1.  En el menú de la izquierda, ve a **APIs y Servicios -> Pantalla de consentimiento de OAuth**.
2.  Selecciona `Externo` y haz clic en `Crear`.
3.  Rellena la información solicitada:
    - **Nombre de la aplicación:** "Asistente n8n" (o el que prefieras).
    - **Correo electrónico de asistencia al usuario:** Tu email.
    - **Datos de contacto del desarrollador:** Tu email.
4.  Guarda y continúa en las siguientes secciones. En "Usuarios de prueba", añade la dirección de Gmail que usarás para las credenciales para poder autenticarte sin que la app necesite ser verificada por Google.

**Paso 3: Crear las Credenciales OAuth 2.0**

1.  Ve a **APIs y Servicios -> Credenciales**.
2.  Haz clic en **+ CREAR CREDENCIALES** y selecciona `ID de cliente de OAuth`.
3.  En **Tipo de aplicación**, selecciona `Aplicación web`.
4.  Asígnale un nombre (ej. "Credencial n8n Web").
5.  En la sección **URIs de redireccionamiento autorizados**, haz clic en `+ AÑADIR URI`. Aquí debes pegar el **URL de redireccionamiento OAuth** de n8n.
    - Para encontrarlo, ve a tu instancia de n8n, a `Credentials -> Add credential`, busca `Google` y copia el `OAuth Redirect URL` que aparece. Suele ser `https://[TU_DOMINIO_N8N]/rest/oauth2-credential/callback`.
6.  Haz clic en `Crear`. Se te mostrará un **ID de cliente** y un **Secreto de cliente**. ¡Cópialos!

**Paso 4: Crear la Credencial en n8n**

1.  En n8n, ve a `Credentials -> Add credential`.
2.  Busca `Google` y selecciónalo.
3.  Pega el `Client ID` y `Client Secret` que obtuviste de Google Cloud.
4.  Haz clic en `Sign in with Google` y autentícate con la cuenta de Google que configuraste como usuario de prueba.
5.  Guarda la credencial (ej. "Mi Google Unificada").

### 2. Credencial de PostgreSQL

1.  En n8n, ve a `Credentials -> Add credential`.
2.  Busca y selecciona `Postgres`.
3.  Rellena los datos de tu base de datos:
    - **Host**: La dirección de tu servidor PostgreSQL.
    - **Port**: El puerto (normalmente `5432`).
    - **User**: Tu usuario de PostgreSQL.
    - **Password**: La contraseña de tu usuario.
    - **Database**: El nombre de la base de datos que usará el bot.
4.  Guarda la credencial.

### 3. Credencial de Redis

1.  En n8n, ve a `Credentials -> Add credential`.
2.  Busca y selecciona `Redis`.
3.  Rellena los datos de tu conexión:
    - **Host**: La dirección de tu servidor Redis.
    - **Port**: El puerto (normalmente `6379`).
    - **Password**: La contraseña de tu servidor Redis (si tiene una).
4.  Guarda la credencial.

### 4. Credencial de Evolution API

1.  En n8n, ve a `Credentials -> Add credential`.
2.  Busca `Evolution API`. Si no aparece, necesitas instalar el [nodo de comunidad](https://www.npmjs.com/package/n8n-nodes-evolution-api).
3.  Rellena los datos de tu instancia:
    - **API URL**: La URL base de tu instancia de Evolution API (ej. `https://api.example.com`).
    - **API Key**: La clave de API que configuraste en tu instancia para la autenticación.
4.  Guarda la credencial.

## 🚀 Puesta en Marcha

1.  **Importar los Flujos:** Descarga los tres archivos `.json` y arrástralos uno por uno a tu editor de flujos de trabajo en n8n para importarlos.
2.  **Asignar Credenciales:**
    - Abre cada flujo de trabajo.
    - Busca los nodos que tengan un campo de credenciales (como `Google Gemini Chat Model`, `Gmail`, `Google Calendar`, `Postgres`, `Evolution API`, etc.).
    - En cada uno de estos nodos, selecciona la credencial correspondiente que creaste en los pasos anteriores.
3.  **Activar los Flujos:** Una vez que todas las credenciales estén asignadas correctamente, activa los tres flujos de trabajo haciendo clic en el interruptor `Active` en la esquina superior izquierda de cada uno.

💬 ¿Cómo Usarlo?
¡Interactuar con el asistente es tan fácil como hablar con un asesor por WhatsApp! El bot está diseñado para que los clientes puedan resolver sus dudas y gestionar sus visitas de manera fluida y natural.

Simplemente envía un mensaje al número de WhatsApp conectado y el asistente se encargará del resto.

Ejemplos de Interacción
Un cliente puede iniciar la conversación y realizar acciones como:

Consultar sobre vehículos:

"Hola, me gustaría saber qué modelos de Audi tienen disponibles."
"¿Podrías darme más detalles del Tesla Model Y? Me interesa la autonomía y el precio."
"Quisiera saber sobre las opciones de financiamiento para un BYD."
Agendar una visita (cita):

"Me gustaría agendar una cita para ver el Ford Mustang Mach-E."
El bot le pedirá los datos necesarios (nombre, email, fecha y hora deseadas).
Automáticamente, el sistema creará el evento en Google Calendar y enviará un email de confirmación al cliente.
Modificar una cita existente:

"Hola, tengo una cita agendada para hoy pero necesito cambiarla para mañana a la misma hora."
"¿Podemos mover mi visita del viernes para el sábado por la mañana?"
Cancelar una visita:

"Necesito cancelar la cita que tenía para esta semana."
"Por favor, elimina mi visita del jueves."
El asistente está programado para interpretar estas solicitudes, usar sus herramientas de calendario y correo de forma autónoma, y responder directamente en el chat. ¡Es un verdadero asesor de ventas y gestor de citas disponible 24/7!