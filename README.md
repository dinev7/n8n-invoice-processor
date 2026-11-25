# n8n-invoice-processor

Flujo de trabajo en n8n que lee facturas en PDF desde Google Drive, extrae datos fiscales e inmobiliarios clave mediante un LLM (Groq / Qwen 3) y guarda cada factura como una fila en Google Sheets.

## Cómo funciona

1. **Búsqueda y descarga:** Obtiene todos los archivos PDF de una carpeta específica de Google Drive.
2. **Extracción de texto:** Extrae el texto sin formato de cada documento PDF.
3. **Extracción con IA:** Utiliza un Agente de IA (`qwen/qwen3.8-27b` a través de Groq) para analizar el emisor, cliente, dirección del inmueble, categoría del gasto, impuestos (IVA/IRPF), consumo de suministros y estado de pago en un formato JSON estricto.
4. **Normalización y filtrado:** Un nodo de JavaScript limpia la salida, asigna las categorías correspondientes y descarta los documentos que no son facturas.
5. **Registro en Sheets:** Añade cada factura procesada como una nueva fila en Google Sheets.

## Requisitos previos

* **n8n** (con soporte para nodos de LangChain / IA)
* Credenciales conectadas para **Google Drive**, **Google Sheets** y **Groq API**

## Configuración

1. Descarga el archivo `.json` del flujo de trabajo de este repositorio e impórtalo en tu instancia de n8n.
2. En el nodo **Search files and folders**, reemplaza el ID de la carpeta de Drive por el tuyo.
3. En el nodo **Append row in sheet**, selecciona tu hoja de cálculo de destino (asegúrate de que los encabezados coincidan con los campos extraídos).
4. Selecciona tus propias credenciales de Google y Groq en sus respectivos nodos.

> **Nota:** El Agente de IA procesa 1 factura cada 30 segundos (*batching*) para mantenerse dentro de los límites de peticiones del plan gratuito de Groq.
