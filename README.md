# Chatbot-Asistente-acad-mico-n8n

Este archivo define un **workflow en n8n** para implementar un asistente académico virtual especializado en **Matemática Aplicada**.  
El asistente integra Google Drive, Google Sheets, Pinecone, OpenAI y PostgreSQL para ofrecer respuestas basadas en recuperación aumentada generativa (RAG) y recursos académicos vinculados al curso.

---

## 🎯 Objetivo del Workflow
- Automatizar la atención a estudiantes registrados en el curso de Matemática Aplicada.  
- Validar códigos de alumno contra registros en Google Sheets.  
- Ofrecer explicaciones académicas usando **Pinecone Vector Store** y **OpenAI**.  
- Compartir recursos adicionales (archivos PDF, teoría, exámenes) vinculados al **silabo**.  
- Mantener memoria conversacional en PostgreSQL para continuidad en las interacciones.

---

## ⚙️ Componentes Principales

### 1. **Triggers**
- **Manual Trigger** (`When clicking ‘Test workflow’`): Permite ejecutar pruebas del flujo.  
- **Chat Trigger** (`When chat message received`): Activa el asistente al recibir un mensaje de alumno.

---

### 2. **Integraciones**
- **Google Drive**  
  - Descarga archivos PDF del curso.  
  - Carpeta configurada: `archivospdf`.  

- **Google Sheets**  
  - `resgistro_alumnos`: Verifica códigos de alumno.  
  - `silabo_temas`: Obtiene unidades, subtemas y recursos vinculados.  

- **Pinecone Vector Store**  
  - Indexa documentos PDF y responde consultas mediante RAG.  

- **OpenAI**  
  - Genera embeddings y respuestas académicas.  
  - Modelo configurado: `gpt-4o`.  

- **Postgres Chat Memory**  
  - Almacena historial conversacional para continuidad.

---

### 3. **Procesamiento de Documentos**
- **Default Data Loader**: Carga documentos binarios.  
- **Recursive Character Text Splitter**: Divide textos en fragmentos para indexación.  
- **Embeddings OpenAI**: Convierte fragmentos en vectores semánticos.  
- **Pinecone Vector Store**: Inserta y consulta embeddings.

---

### 4. **Agente Académico (AI Agent)**
El nodo central que define el comportamiento del asistente:

- Rol: **Compañero de estudio digital** cálido, formal y empático.  
- Flujo de atención:
  1. Solicita código de alumno.  
  2. Valida en `resgistro_alumnos`.  
  3. Si válido: saluda y muestra temas del curso.  
  4. Responde consultas usando RAG y entrega recursos del `silabo`.  

- Formato matemático:  
  - Fórmulas en línea: `$...$`  
  - Fórmulas en bloque: `$$...$$`

---

## 🔗 Conexiones Principales

| Nodo origen                | Nodo destino            | Tipo conexión     |
|-----------------------------|-------------------------|------------------|
| Manual Trigger              | Google Drive            | main             |
| Google Drive                | descargar3              | main             |
| descargar3                  | Loop Over Items3        | main             |
| Loop Over Items3            | Pinecone Vector Store   | main             |
| Embeddings OpenAI7          | Pinecone Vector Store   | ai_embedding     |
| Default Data Loader7        | Pinecone Vector Store   | ai_document      |
| Recursive Text Splitter7    | Default Data Loader7    | ai_textSplitter  |
| Chat Trigger                | AI Agent                | main             |
| resgistro_alumnos           | AI Agent                | ai_tool          |
| silabo_temas                | AI Agent                | ai_tool          |
| Pinecone Vector Store3      | AI Agent                | ai_tool          |
| OpenAI Chat Model2          | AI Agent                | ai_languageModel |
| Postgres Chat Memory        | AI Agent                | ai_memory        |

---

## 📎 Flujo Académico Ejemplificado
1. Alumno envía código → validación en `resgistro_alumnos`.  
2. Si válido → saludo + listado de temas del `silabo`.  
3. Alumno consulta un subtema → respuesta generada con RAG desde Pinecone.  
4. Si hay recursos vinculados (teoría/examen) → se entregan automáticamente.  
5. Conversación se guarda en PostgreSQL para continuidad.

---

## 🚀 Buenas Prácticas
- Mantener actualizado el **silabo** en Google Sheets para que el asistente entregue recursos correctos.  
- Revisar la carpeta `archivospdf` en Google Drive para asegurar que los documentos estén indexados.  
- Usar nombres claros en los nodos para facilitar mantenimiento.  
- Validar credenciales de Google, Pinecone y OpenAI antes de activar el flujo.  

