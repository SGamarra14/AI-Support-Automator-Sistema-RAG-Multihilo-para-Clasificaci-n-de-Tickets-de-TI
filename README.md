# AI-Support-Automator: Sistema RAG & LangGraph para Soporte de TI

Este proyecto implementa un pipeline avanzado de **IA Agéntica** diseñado para automatizar la extracción de datos y la resolución de incidentes en departamentos de soporte técnico de TI. El sistema procesa transcripciones de conversaciones de soporte, identifica información clave y recomienda soluciones basadas en una base de conocimientos histórica.

## 🚀 Características Principales

* **Arquitectura RAG (Retrieval-Augmented Generation):** Utiliza `text-embedding-ada-002` para vectorizar una bitácora histórica de 80 soluciones técnicas, permitiendo al modelo consultar experiencias pasadas antes de responder.
* **Orquestación con LangGraph:** Implementa un flujo de trabajo basado en grafos de estado (`StateGraph`) para gestionar la lógica de procesamiento y la agregación de resultados de forma estructurada.
* **Procesamiento en Paralelo y por Lotes:** Optimiza el rendimiento procesando múltiples conversaciones simultáneamente en lotes de 16, gestionando los *rate limits* de la API con pausas controladas.
* **Extracción de Datos Estructurados:** Transforma diálogos informales en objetos JSON validados que incluyen ID de Ticket, área, persistencia del problema y clasificación de hardware/software.
* **Filtrado por Similitud Semántica:** Implementa un umbral de confianza (similitud de cosenos > 0.78) para asegurar que solo se inyecte contexto RAG verdaderamente relevante al modelo.
* **Generación de Reportes:** Exporta automáticamente todos los incidentes procesados a un archivo Excel (`Reporte_Tickets_TI.xlsx`) para auditoría y toma de decisiones.

## 🛠️ Stack Tecnológico

* **LLM:** GPT-4o-mini.
* **Frameworks:** LangChain, LangGraph.
* **Embeddings:** OpenAI API (`text-embedding-ada-002`).
* **Procesamiento de Datos:** Pandas, NumPy, JSON.
* **Infraestructura:** Python, Dotenv.

## 📋 Requisitos Previos

1.  Clonar el repositorio.
2.  Crear un archivo `.env` con tu `OPENAI_API_KEY`.
3.  Instalar las dependencias necesarias:
    ```bash
    pip install langchain-openai python-dotenv pdfplumber langgraph pandas numpy
    ```

## 📖 Cómo Funciona el Pipeline

1.  **Ingesta y Limpieza:** El sistema carga las transcripciones (`transcripciones.json`) y la bitácora histórica (`historico.json`).
2.  **Generación de Embeddings:** Se crean vectores de las soluciones históricas para permitir búsquedas semánticas eficientes.
3.  **Ejecución del Grafo:** Para cada lote de conversaciones, se crea un nodo por conversación que:
    * Genera un embedding de la consulta actual.
    * Busca la solución más parecida en la base RAG.
    * Invoca al LLM con un prompt especializado que incluye el contexto histórico recuperado.
4.  **Consolidación:** Los resultados de todos los nodos se aplanan, se limpian de formatos Markdown y se guardan en un reporte final de Excel.

## 📊 Resultados Generados

El sistema produce un archivo `Reporte_Tickets_TI.xlsx` con las siguientes columnas extraídas automáticamente:
* Nombre del Usuario e ID.
* Resumen del problema y Fecha.
* Tipo de incidencia (Hardware/Software).
* Recomendación de solución basada en RAG (o escalamiento a Nivel 2).

---
*Proyecto desarrollado como parte del curso de implementación de soluciones de IA.*
