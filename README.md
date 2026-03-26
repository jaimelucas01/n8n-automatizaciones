# n8n-automatizaciones

Flujo 1 — Analizador automático de CVs con IA
¿Qué hace?
Sistema de preselección de candidatos que recibe CVs en PDF, los analiza con IA y carga automáticamente una puntuación y opinión de cada candidato en un Google Sheet — sin que ningún humano tenga que leer el CV primero.
Cómo funciona por dentro:

Un formulario web recibe el CV del candidato en PDF
Extrae el texto del PDF y lo procesa en paralelo con dos agentes de IA: uno extrae los datos personales (nombre, email, teléfono, dirección) y el otro extrae formación académica, historial laboral y habilidades
Combina los resultados de ambos agentes y genera un resumen profesional del candidato de máximo 100 palabras, escrito desde la perspectiva de un reclutador senior
Con ese resumen, un LLM evalúa al candidato contra el perfil buscado y devuelve una puntuación del 1 al 10 con una justificación detallada en formato JSON estructurado
Todo se guarda automáticamente en un Google Sheet con nombre, contacto, habilidades, experiencia, estudios, puntuación y opinión del agente

Stack:

n8n
OpenAI GPT-4o-mini (extracción de información + resumen + evaluación)
n8n Form Trigger (formulario de carga de CV)
Extracción de texto desde PDF
Google Sheets (base de datos de candidatos)
Structured Output Parser (respuesta JSON tipada)

#Flujo 2 — Agente de atención al cliente para Instagram
¿Qué hace?
Automatización completa que recibe mensajes y comentarios de Instagram en tiempo real, los analiza con IA y responde automáticamente según el tipo de mensaje — sin intervención humana.
Cómo funciona por dentro:

Un webhook escucha los DMs y comentarios que llegan a la cuenta de Instagram
Extrae el texto del mensaje y verifica que haya contenido para procesar
Analiza el sentimiento del mensaje con GPT-4o-mini
Si el mensaje es positivo o una consulta general → un agente de IA responde directamente con info de la tienda (precios, envíos, formas de pago)
Si el mensaje es negativo → lo clasifica entre RECLAMO o AGRESIVO
Si es un reclamo → otro agente responde con empatía y avisa que un humano va a contactarlo
Si es agresivo → manda una alerta por email al equipo y no responde automáticamente
La respuesta se envía de vuelta al usuario vía la API de Meta

Stack:

n8n
OpenAI GPT-4o-mini (análisis de sentimiento + 3 agentes distintos)
Meta Graph API (Instagram)
Webhook
SMTP / Email
JavaScript (nodos de código personalizados)

#Flujo 3 — Automatización de envíos con Andreani para e-commerce
¿Qué hace?
Sistema completo que todos los días a las 7AM busca automáticamente los pedidos pagados en Shopify (compatible también con Tienda Nube y WooCommerce), genera las órdenes de envío en Andreani, y cuando llega el PDF de etiquetas lo procesa para extraer los números de tracking y notificar a cada cliente.
Cómo funciona por dentro:
El flujo tiene dos partes:
Parte 1 — Carga automática de envíos (se ejecuta cada mañana):

Un trigger CRON dispara el flujo de lunes a sábado a las 7AM
Consulta la API de Shopify para traer todos los pedidos pagados y sin despachar
Normaliza los datos de dirección al formato que requiere Andreani (incluyendo conversión de provincias a códigos ISO)
Compara contra un Google Sheet para filtrar pedidos que ya fueron procesados y evitar duplicados
Por cada pedido nuevo genera la orden de envío en la API de Andreani, con un wait de 3 segundos entre cada llamada para respetar el rate limit
Si el envío se crea bien lo registra en el Sheet. Si falla, manda un email de alerta con el detalle del error para cargarlo manualmente

Parte 2 — Procesamiento del PDF de etiquetas:

Un webhook recibe el PDF de etiquetas que genera Andreani
Extrae el texto del PDF y parsea los números de tracking con regex
Cruza cada tracking con el pedido correspondiente en el Google Sheet
Actualiza el estado del pedido en Shopify con el número de tracking
Manda un email automático al cliente avisando que su pedido fue despachado
Registra todo en el Sheet con el estado final

Stack:

n8n
Shopify API / Tienda Nube API / WooCommerce API
Andreani API
Google Sheets (como base de datos de log)
SMTP / Email
JavaScript (normalización de datos, regex para parseo de PDF, manejo de rate limits)
CRON scheduling
Webhooks
