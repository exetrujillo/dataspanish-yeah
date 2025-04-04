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

## Notebook 1: `Dataset_spanish_yeah.ipynb` - Análisis Lingüístico Detallado

**Propósito:** Tomar una lista de frases en español y generar un análisis profundo para cada una, incluyendo:

1.  **Cadena de Pensamiento (CoT):** Un análisis paso a paso generado por un modelo LLM (Gemini 1.5 Flash/Pro) que identifica componentes, ambigüedades, contexto inferido y significado.
2.  **Respuesta JSON Estructurada:** Un análisis detallado palabra por palabra, guiado por el CoT, que extrae información morfológica y semántica según schemas predefinidos (sustantivo, verbo, adjetivo, etc.).

**Flujo de Trabajo:**

1.  **Celda 0-1: Instalación y Configuración:**
    *   Instala la librería `google-generativeai`.
    *   Importa las librerías necesarias (`pandas`, `json`, `re`, `time`, `os`, `genai`).
    *   **Configura tu `GOOGLE_API_KEY`:** **Importante:** Añade tu API Key a los "Secrets" de Colab con el nombre `GOOGLE_API_KEY` (icono de llave en el panel izquierdo). El código intentará cargarla desde ahí.
    *   Define el modelo LLM a usar (ej: `gemini-1.5-flash`).
2.  **Celda X (Opcional): Cargar Frases desde Archivo:**
    *   Si prefieres usar tus propias frases desde un archivo `.txt` o `.csv` en lugar de la lista predefinida, descomenta y configura esta celda para cargar tu archivo.
3.  **Celda 3: Definir Lista de Frases:**
    *   Contiene una lista predefinida de frases (`frases_para_analizar`) con diversos fenómenos lingüísticos (ambigüedad, coloquialismos, complejidad, etc.). Puedes modificar o reemplazar esta lista. Si usaste la Celda X, esta celda puede ser omitida o su contenido será sobrescrito.
4.  **Celda 4: Procesamiento Principal:**
    *   Define los schemas JSON para cada categoría gramatical.
    *   Define funciones auxiliares (como `parse_cot`).
    *   **Bucle Principal:** Itera sobre cada `frase_para_analizar`.
        *   **Genera CoT:** Llama al modelo LLM con un prompt específico para generar la Cadena de Pensamiento detallada sobre la frase.
        *   **Genera JSON:** Parsea la identificación inicial de palabras del CoT y llama de nuevo al LLM con otro prompt (que incluye la frase, el CoT generado y los schemas) para producir la respuesta JSON estructurada palabra por palabra.
        *   Guarda la frase, el CoT y la respuesta JSON (como string) en una lista de resultados.
        *   Incluye pausas (`time.sleep`) para evitar exceder los límites de la API.
5.  **Celda 5: Exportar Resultados a CSV:**
    *   Convierte la lista de resultados en un DataFrame de Pandas.
    *   Guarda/Añade los resultados al archivo `diccionario_linguistico_output.csv` en el directorio de Colab. Si el archivo ya existe, añade las nuevas filas; si no, lo crea.
6.  **Celda 6: Cargar y Mostrar Fila Específica:**
    *   Permite cargar el CSV generado y mostrar el contenido completo (Frase, CoT, JSON formateado) de una fila específica introduciendo su índice. Útil para inspeccionar resultados individuales.

**Salida Principal:**

*   El archivo `diccionario_linguistico_output.csv` que contiene la frase original, la Cadena de Pensamiento generada, y la respuesta JSON estructurada para cada frase procesada. **Este archivo es el input principal para el segundo notebook.**

## Notebook 2: `data_spanish_wow.ipynb` - Visualización Semántica Interactiva

**Propósito:** Cargar el archivo CSV generado por el primer notebook y crear visualizaciones interactivas en 2D y 3D que muestren las relaciones semánticas entre las *palabras* de una frase seleccionada, utilizando embeddings de Gemini.

**Flujo de Trabajo:**

1.  **Celda 0: Configuración Inicial y Librerías:**
    *   Instala librerías adicionales: `pandas`, `numpy`, `scikit-learn`, `plotly`, `umap-learn`, `nltk`.
    *   Importa las librerías necesarias.
    *   **Configura tu `GOOGLE_API_KEY`:** Igual que en el primer notebook, cárgala desde los Secrets.
    *   Descarga el recurso `punkt` de NLTK (aunque su uso para segmentación fue reemplazado por el LLM en el flujo actual).
    *   Define los modelos a usar: `EMBEDDING_MODEL_NAME` (ej: `embedding-001` o el experimental si funciona) y `LLM_MODEL_NAME` (para el Meta-CoT).
2.  **Celda 1: Carga, Limpieza y Exploración del CSV:**
    *   **Sube tu archivo `diccionario_linguistico_output.csv`** (o como lo hayas llamado) a la carpeta `sample_data` en Colab.
    *   Configura el nombre del archivo y los nombres de las columnas (`frase`, `Complex.CoT`, `respuesta`).
    *   Carga el CSV en un DataFrame `df_analisis`.
    *   **Limpia Duplicados:** Elimina filas que sean *exactamente idénticas* en todas las columnas.
    *   Muestra información general y una fila de muestra aleatoria.
3.  **Celda 2: Selección Interactiva de la Frase/Texto:**
    *   Crea un menú desplegable (`ipywidgets.Dropdown`) con las frases/textos únicos presentes en `df_analisis`.
    *   **El usuario debe seleccionar una entrada** de este menú para analizarla visualmente.
4.  **Celda 3 (Modificado): Recuperar Datos, Segmentar Texto y Generar Meta-CoT:**
    *   Recupera la fila completa (CoT original, JSON original) correspondiente al texto seleccionado en el dropdown.
    *   Parsea el string JSON original a un objeto Python (`parsed_json`).
    *   Llama al modelo LLM con un prompt específico que le pide:
        *   **Segmentar** el texto seleccionado en frases/unidades significativas (si contiene más de una).
        *   Generar un **Meta-Análisis CoT** conciso que resuma los aspectos semánticos y contextuales clave del análisis original.
    *   Procesa la respuesta del LLM para extraer la `lista_unidades_detectadas` y el `meta_cot_resumen`. Muestra ambos.
5.  **Celda 4 (Revisado v3): Preparación y Generación de Embeddings (Palabras con Detalles JSON y Unidades):**
    *   Extrae la información detallada de **cada palabra** del `parsed_json` (incluyendo categoría, lema, género, tiempo, etc., según los schemas).
    *   Prepara una lista unificada (`lista_unificada_textos`) que contiene:
        *   El texto de cada palabra individual.
        *   El texto de cada unidad detectada (si hay > 1).
        *   El texto completo original.
    *   Llama a la API de embeddings (`get_embeddings_batch_local`) para obtener un vector para cada elemento de la lista unificada.
    *   Estructura los resultados en un DataFrame `df_embeddings_combinado`, guardando el texto, el tipo ('Palabra', 'Unidad', 'TextoCompleto'), el índice de referencia, el embedding, y **todos los detalles JSON** extraídos para las palabras.
6.  **Celda 5 (Revisado v2): Reducción de Dimensionalidad (2D):**
    *   Toma todos los embeddings válidos de `df_embeddings_combinado`.
    *   Aplica UMAP y PCA para reducir la dimensionalidad a 2 componentes (X, Y).
    *   Añade las columnas `umap_x`, `umap_y`, `pca_x`, `pca_y` a `df_embeddings_combinado`.
7.  **Celda 6 (Revisado v3): Creación del DataFrame Final con Tooltip Dinámico:**
    *   Copia `df_embeddings_combinado` a `df_plot_final`.
    *   Define y aplica la función `format_tooltip_details` para crear una nueva columna `tooltip_details`. Esta columna contiene un string HTML formateado con los detalles lingüísticos relevantes (y no nulos) para cada palabra, extraídos de las columnas JSON.
    *   Asegura que `df_plot_final` tenga todas las columnas necesarias para las visualizaciones 2D y 3D.
8.  **Celda 7 (Revisado v4 - Mejorado): Visualización 2D + Distribución de Categorías:**
    *   Permite seleccionar coordenadas UMAP o PCA 2D.
    *   Crea un gráfico de dispersión 2D interactivo (`plotly.graph_objects`):
        *   Muestra cada palabra como un punto, coloreado por su `categoria`.
        *   Muestra marcadores especiales (opcionales) para 'Unidad Detectada' (cruz) y 'Texto Completo' (diamante).
        *   Conecta las palabras en secuencia con una línea semitransparente.
        *   Conecta el 'Texto Completo' a la primera palabra con una línea punteada.
        *   Usa la columna `tooltip_details` para mostrar información detallada y formateada al pasar el ratón sobre cada palabra.
    *   Crea un gráfico de barras adicional mostrando la frecuencia de cada categoría gramatical, usando los mismos colores que el scatter plot.
    *   Muestra el Meta-CoT y la frase original como contexto.
9.  **Celda 8 (Revisado): Guía de Interpretación (Mapa de Palabras):**
    *   Celda Markdown que explica cómo interpretar el gráfico 2D de palabras, considerando la proximidad, los colores de categoría, las líneas de secuencia y las limitaciones.
10. **Celda 9: Reducción de Dimensionalidad a 3D:**
    *   Similar al Chunk 5, pero reduce los embeddings combinados a 3 componentes (X, Y, Z) usando UMAP y PCA.
    *   Añade las columnas `*_x_3d`, `*_y_3d`, `*_z_3d` a `df_plot_final`.
11. **Celda 10 (v4 - Final): Visualización Interactiva en 3D:**
    *   Permite seleccionar coordenadas UMAP o PCA 3D.
    *   Crea un gráfico de dispersión 3D interactivo (`go.Scatter3d`):
        *   Replica la estructura del gráfico 2D (puntos de palabras coloreados, marcadores especiales, líneas de conexión).
        *   Utiliza la columna `tooltip_details` para proporcionar los mismos tooltips informativos que en 2D.
    *   Permite rotar, hacer zoom y panear para explorar la estructura en 3D.

**Salida Principal:**

*   Visualizaciones interactivas (2D y 3D) en Plotly que permiten explorar las relaciones semánticas entre las palabras de un texto seleccionado, con información contextual y lingüística detallada disponible en los tooltips.
