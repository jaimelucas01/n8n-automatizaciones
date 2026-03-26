# n8n Automatizaciones

Flujos de automatización reales construidos con n8n, integrando IA, APIs externas, webhooks y bases de datos. Todos los flujos están en producción o fueron desarrollados para clientes reales.

---

## Flujo 1 — Analizador automático de CVs con IA

**¿Qué hace?**  
Sistema de preselección de candidatos que recibe CVs en PDF, los analiza con IA y registra automáticamente una puntuación y opinión de cada candidato en un Google Sheet, sin que ningún humano tenga que leer el CV primero.

**Cómo funciona:**
- Un formulario web recibe el CV del candidato en PDF
- Extrae el texto y lo procesa en paralelo con dos agentes de IA: uno extrae datos personales y el otro extrae formación, historial laboral y habilidades
- Genera un resumen profesional del candidato escrito desde la perspectiva de un reclutador senior
- Un LLM evalúa al candidato contra el perfil buscado y devuelve una puntuación del 1 al 10 con justificación en formato JSON estructurado
- Todo se guarda automáticamente en Google Sheets

**Stack:** `n8n` `OpenAI GPT-4o-mini` `Google Sheets` `PDF Extraction` `Structured Output Parser`

<img width="1381" height="615" alt="Analizador-de-CV- n8n" src="https://github.com/user-attachments/assets/0adf5f79-fb49-417f-8e4a-38793e6bf0ad" />

---

## Flujo 2 — Agente de atención al cliente para Instagram

**¿Qué hace?**  
Automatización completa que recibe mensajes y comentarios de Instagram en tiempo real, los analiza con IA y responde automáticamente según el tipo de mensaje, sin intervención humana.

**Cómo funciona:**
- Un webhook escucha los DMs y comentarios que llegan a la cuenta
- Analiza el sentimiento del mensaje con GPT-4o-mini
- Si es una consulta general → un agente responde con info de la tienda (precios, envíos, formas de pago)
- Si es negativo → lo clasifica entre RECLAMO o AGRESIVO
- Si es un reclamo → otro agente responde con empatía y avisa que un humano va a contactarlo
- Si es agresivo → manda una alerta por email al equipo y no responde automáticamente
- La respuesta final se envía al usuario vía la API de Meta

**Stack:** `n8n` `OpenAI GPT-4o-mini` `Meta Graph API` `Webhooks` `SMTP` `JavaScript`

<img width="1563" height="704" alt="Automatización-IG" src="https://github.com/user-attachments/assets/0cd72872-020f-415b-aaf4-522f217ec196" />

---

## Flujo 3 — Automatización de envíos con Andreani para e-commerce

**¿Qué hace?**  
Sistema completo que cada mañana busca los pedidos pagados en Shopify, genera las órdenes de envío en Andreani automáticamente, y cuando llega el PDF de etiquetas lo procesa para extraer los números de tracking y notificar a cada cliente.

**Cómo funciona:**

*Parte 1 — Carga automática de envíos (se ejecuta cada mañana):*
- Un trigger CRON dispara el flujo de lunes a sábado a las 7AM
- Consulta la API de Shopify para traer pedidos pagados y sin despachar
- Normaliza los datos de dirección al formato de Andreani (con conversión de provincias a códigos ISO)
- Filtra duplicados comparando contra un Google Sheet de log
- Genera la orden de envío en Andreani con un wait de 3s entre llamadas para respetar el rate limit
- Si falla, manda un email de alerta con el detalle del error

*Parte 2 — Procesamiento del PDF de etiquetas:*
- Un webhook recibe el PDF de etiquetas de Andreani
- Extrae el texto y parsea los números de tracking con regex
- Cruza cada tracking con el pedido en el Google Sheet
- Actualiza el estado en Shopify con el número de tracking
- Manda un email automático al cliente avisando el despacho

**Stack:** `n8n` `Shopify API` `Andreani API` `Google Sheets` `SMTP` `JavaScript` `CRON` `Webhooks`

<img width="1550" height="720" alt="Envios-automaticos-Ecommerce" src="https://github.com/user-attachments/assets/ff75a788-5d5c-4606-b657-f01198a70c33" />


---

## Stack general

| Herramienta | Uso |
|---|---|
| n8n | Orquestación de todos los flujos |
| OpenAI GPT-4o-mini | Análisis, clasificación y generación de texto |
| Google Sheets | Base de datos y logging |
| Meta Graph API | Integración con Instagram |
| Andreani API | Generación de envíos |
| Shopify API | Gestión de pedidos |
| Webhooks | Integración de eventos externos |
| JavaScript | Lógica personalizada en nodos de código |
