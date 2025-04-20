# Configurar entorno Local:
Es necesario tener una instancia de LangFlow ya que es una Aplicación Web:

https://github.com/langflow-ai/langflow

https://www.langflow.org/

Despues de configurar y acceder a la interfaz de LangFlow se puede importar el archivo "Micardis Pharmaceutical Agent.json"
para cargar el proyecto.

# LangFlow-TinyLLAMA
Construcción de un agente farmacéutico haciendo  uso de LangFlow para modelar un RAG que utiliza un LLM (Tiny-LLAMA) 


¡Por supuesto! Aquí tienes tu texto corregido ortográficamente y con algunos ajustes de puntuación y estilo para mayor claridad:

---

La herramienta sigue en desarrollo, así que se recomienda revisar la documentación original.  
[Documentación de LangFlow](https://docs.langflow.org/)

**LangFlow** es una herramienta para modelar **RAGs** y flujos de **LLM** de forma visual.  
En este documento se mostrará el uso de la herramienta para crear un **RAG** para un agente al que se le hacen preguntas sobre el medicamento para la presión **Micardis**.

---

### Herramientas usadas:

- API de Ollama instalado localmente
    
- [Tiny-LLAMA](https://github.com/jzhang38/TinyLlama), un LLM de 3 millones de parámetros
    
- Knowledge Base (documento PDF estructurado)
    
- Astra DB como base de datos vectorial
    
- [Nomic-Embedding](https://ollama.com/library/nomic-embed-text)
    

---

## Definición de RAG

Un **RAG (Retrieval-Augmented Generation)** es una técnica que combina modelos de lenguaje grandes (**LLMs**) con sistemas de recuperación de información para mejorar la generación de respuestas.

En lugar de depender solo de lo que el modelo "sabe" internamente, el RAG busca información **externa y actualizada** (por ejemplo, en una base de datos o documentos) y la utiliza como contexto para generar respuestas más precisas y relevantes.

---

## Componentes de LangFlow

Una de las grandes ventajas de esta herramienta es que se pueden visualizar las conexiones del flujo y apreciar el funcionamiento del RAG. (Los colores indican diferentes objetos que son retornados por cada componente).


---

## Implementación de una base de datos vectorial

Un flujo básico toma el input del usuario y lo envía al componente especial para **Prompt Engineering**, que agregará el contexto obtenido desde la base de datos vectorial **Astra DB**.

Con esto en mente, el flujo incorpora:

- Módulo **Astra DB**, que conecta con la base de datos.
    
- **Ollama Embeddings**, que es el componente que permite conectar con la API de Ollama para usar algún modelo de embeddings.
    
- Este se encarga de hacer la transformación y agrupación de las palabras en vectores.
    
- Componente **File**, que permite cargar el archivo (se guarda en el contenedor Docker).
    
- Un componente **Split Text** para dividir el archivo en partes, previo a ser ingerido en la base de datos.
    

Finalmente, es necesario un **Parser** que extraiga el **texto** del resultado de la búsqueda semántica.

---

## Prompt Template

Este es el prompt template utilizado, que cuenta con un segmento `{context}`, el cual representa la información de la **Knowledge Base**.

```
You will have the following Role:
You are a virtual pharmaceutical assistant specialized in helping users understand Micardis, a medication prescribed for high blood pressure. Your only role is to answer questions about Micardis using the provided context.
Make sure to follow the rules and answer user question reading the text below.

Rules for Responses:
Only answer questions about Micardis (uses, dosage, side effects, precautions).
If the user asks unrelated questions or you lack info:

"I’m sorry, I don’t have that information. Feel free to ask about Micardis—like how to take it or its side effects!"

NOTE If the user asks directly/indirectly:
"Who are you?"
"What can you do?"

Reply immediately (word-for-word):
"Welcome! I’m your pharmaceutical assistant here to help with Micardis, your prescribed medication. Ask me about its purpose, how to take it, or safety tips!"

Tone & Behavior:
Professional but friendly: Use plain language (no jargon unless explained).
Empathetic: Acknowledge concerns (e.g., "I understand managing blood pressure is important—let’s clarify how Micardis works.").

Context: {context}

Me question is : {question}
```

---

## Diseño del RAG

El RAG que se muestra primero toma la pregunta del usuario y hace una **búsqueda semántica** en la base de datos vectorial. Luego, el resultado de la búsqueda se agrega al contexto del prompt y se envía al modelo.

<img width="961" alt="Screenshot 2025-04-19 at 20 08 39" src="https://github.com/user-attachments/assets/e3b1e55b-26c4-4208-8ada-8b0c69904ef9" />


<img width="1149" alt="Screenshot 2025-04-19 at 20 03 13" src="https://github.com/user-attachments/assets/da536dcc-33b5-48ac-a0b2-14d3e269287d" />
