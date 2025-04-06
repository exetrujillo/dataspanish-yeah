# dataspanish-yeah
Notebooks para la creación de datasets para estudios lingüísticos usando GEMINI API

# Procesamiento y Visualización de Datos Lingüísticos con Gemini

Este repositorio contiene dos notebooks de Google Colab diseñados para analizar y visualizar datos lingüísticos de frases en español, utilizando la API de Google Gemini.

1.  **`dataspanish_yeah.ipynb`**: Realiza un análisis lingüístico detallado de cada frase de entrada.
2.  **`dataspanish_wow.ipynb`**: Carga los resultados del primer notebook y genera visualizaciones interactivas (2D y 3D) de los embeddings semánticos de las palabras dentro de cada frase.

## Requisitos Previos

*   **Cuenta de Google:** Necesaria para usar Google Colab.
*   **API Key de Google Gemini:** Debes obtener una API key desde [Google AI Studio](https://aistudio.google.com/) (o el proveedor correspondiente).
*   **Archivo de Frases (Opcional):** Si no quieres usar la lista de frases predefinida en `Dataset_spanish_yeah.ipynb`, puedes preparar tu propio archivo `.txt` o `.csv` con las frases a analizar.

## Notebook 1: `dataspanish_yeah.ipynb` - Análisis Lingüístico Detallado

**Propósito:** Tomar una lista de frases en español y generar un análisis profundo para cada una, incluyendo:

1.  **Cadena de Pensamiento (CoT):** Un análisis paso a paso generado por un modelo LLM (Gemini Flash/Pro) que identifica componentes, ambigüedades, contexto inferido y significado.
2.  **Respuesta JSON Estructurada:** Un análisis detallado palabra por palabra, guiado por el CoT, que extrae información morfológica y semántica según schemas predefinidos (sustantivo, verbo, adjetivo, etc.).

## Notebook 2: `dataspanish_wow.ipynb` - Visualización Semántica Interactiva

**Propósito:** Cargar los archivos generados por el primer notebook y crear visualizaciones interactivas en 2D y 3D que muestren las relaciones semánticas entre las *palabras* de una frase seleccionada, entre textos, segmentos, etc.

**Salida Principal:**

*   Visualizaciones interactivas (2D y 3D) en Plotly que permiten explorar las relaciones semánticas entre las palabras de un texto seleccionado, con información contextual y lingüística detallada disponible en los tooltips.
