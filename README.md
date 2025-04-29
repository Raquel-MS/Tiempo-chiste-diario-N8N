# n8n Workflow: Tiempo y Chiste Diarios en Telegram

Este workflow de n8n automatiza el envío diario de la predicción meteorológica de una zona específica y un chiste a un canal de Telegram.

## Funcionalidad

1.  **Obtiene la predicción meteorológica:**
    * Utiliza un nodo **HTTP Request** para consultar la API de la Agencia Estatal de Meteorología (AEMET) y obtener la predicción diaria para un municipio específico (configurado en el nodo).
    * Un nodo **Function** (denominado "Code") parsea la respuesta JSON de la API de AEMET y extrae la información relevante: nombre del municipio, temperatura máxima y mínima, probabilidad de lluvia y avisos meteorológicos.

2.  **Obtiene un chiste:**
    * Utiliza otro nodo **HTTP Request** para consultar una API de chistes (en este caso, `https://v2.jokeapi.dev/joke/Any?blacklistFlags=nsfw,religious,political,racist,sexist,explicit&safe-mode`).
    * El nodo **Merge** combina los resultados de la predicción meteorológica y el chiste en un solo objeto para el siguiente nodo.

3.  **Formatea el mensaje para Telegram:**
    * Un nodo **Function** (denominado "Code mensaje") toma la información combinada y genera un mensaje con formato Markdown optimizado para Telegram. Este mensaje incluye:
        * Un saludo con un emoji representativo del clima.
        * El nombre del municipio en negrita.
        * La temperatura máxima y mínima en negrita, con el emoji de termómetro.
        * La probabilidad de lluvia con el emoji de la lluvia.
        * Los avisos meteorológicos (si los hay) con el emoji de advertencia.
        * Un chiste con formato de código.

4.  **Envía el mensaje a Telegram:**
    * Finalmente, un nodo **Telegram** envía el mensaje formateado a un canal o chat de Telegram específico (la conexión y el ID del chat deben estar configurados en el nodo de Telegram).

5.  **Activación diaria:**
    * Un nodo **Schedule Trigger** está configurado para ejecutar este workflow automáticamente cada día a una hora específica (configurable en el nodo).

## Requisitos

Para utilizar este workflow, necesitarás:

* **n8n instalado y configurado.**
* **Una clave de API de AEMET.** Puedes obtener una de forma gratuita en el sitio web de AEMET Open Data. Deberás configurar esta clave en el nodo **HTTP Request** que consulta la API de AEMET.
* **Una cuenta de Telegram y un bot de Telegram creado.** Necesitarás el token del bot para configurar la conexión en el nodo **Telegram**.
* **El ID del chat o canal de Telegram** donde deseas enviar los mensajes.

## Configuración

1.  **Importar el workflow:** Importa el archivo JSON de este workflow a tu instancia de n8n.
2.  **Configurar la API de AEMET:**
    * Selecciona el nodo **HTTP Request** que consulta la API de AEMET .
    * En los parámetros de la petición, localiza el campo para la `api_key` y reemplaza {{$env.AEMET_API_KEY}}) con tu clave de API de AEMET.
    * Asegúrate de que la URL esté configurada para el municipio deseado. El ID del municipio para Rivas-Vaciamadrid (ejemplo en la captura) es `28123`. Puedes cambiar este ID para obtener la predicción de otra localidad.
3.  **Configurar la conexión de Telegram:**
    * Selecciona el nodo **Telegram**.
    * Crea una nueva conexión de Telegram si no tienes una configurada. Necesitarás el token de tu bot de Telegram.
    * En los parámetros del nodo Telegram, especifica el **Chat ID** del canal o chat al que deseas enviar los mensajes.
4.  **Configurar el horario de activación:**
    * Selecciona el nodo **Schedule Trigger**.
    * Configura la hora del día a la que deseas que el workflow se ejecute diariamente.
5.  **(Opcional) Editar el formato del mensaje:**
    * Selecciona el nodo **Function** denominado "Code mensaje".
    * Puedes modificar el código JavaScript para personalizar el formato del mensaje de Telegram, los emojis utilizados o la información que se incluye.

## Uso

Una vez configurado, este workflow se ejecutará automáticamente a la hora programada cada día. Enviará un mensaje con la predicción meteorológica de la zona especificada y un chiste al canal de Telegram configurado.

## Notas

* Asegúrate de que tu instancia de n8n tenga acceso a las APIs externas (AEMET y la API de chistes) y a los servidores de Telegram.
* Puedes personalizar el workflow para obtener información adicional de la API de AEMET o utilizar diferentes APIs de chistes.
* Considera añadir nodos de error handling para gestionar posibles fallos en las peticiones a las APIs.
