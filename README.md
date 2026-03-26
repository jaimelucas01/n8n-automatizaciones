# n8n-automatizaciones
Flujo 2 — Agente de atención al cliente para Instagram
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
