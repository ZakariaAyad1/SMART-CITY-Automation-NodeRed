# Sistema de Analítica de Potabilidad del Agua en Node-RED

Este repositorio contiene un flujo avanzado de **Node-RED** diseñado para la ingesta, procesamiento estadístico y exposición vía API de datos sobre la potabilidad del agua. El sistema está optimizado para operar de forma autónoma, utilizando el contexto global para el almacenamiento en caché de analíticas complejas sin depender de bases de datos externas.

## 📊 Descripción General

El proyecto implementa un ecosistema de procesamiento de datos estructurado en tres capas funcionales:

1.  **Ingesta y Persistencia Temporal:** Carga de datasets en formato CSV, tipado dinámico de atributos y almacenamiento en el contexto global de Node-RED.
2.  **Motor de Analítica Descriptiva:** Computación de métricas estadísticas (media, desviación típica, cuantiles), correlaciones de Pearson, histogramas de frecuencia y detección de anomalías mediante *z-score*.
3.  **Capa de Servicios (HTTP API):** Interfaz REST que permite la consulta externa de las analíticas procesadas y el muestreo aleatorio de registros.

## 🛠 Estructura del Flujo

El flujo se divide en tres pestañas (tabs) principales para garantizar la modularidad:

### 1. Dataset - Load & Cache :
Encargada del ciclo de vida de los datos. Permite definir la ruta del archivo fuente (`water_potability_cleaned.csv`), realizar el *parsing* y persistir los registros en memoria para optimizar el rendimiento de las consultas subsiguientes.

### 2. Dataset - Analytics
Constituye el núcleo algorítmico del sistema. Realiza:
* **Métricas por Potabilidad:** Diferenciación de medias entre agua potable y no potable.
* **Análisis de Correlación:** Identificación de la influencia de factores como el pH, Sulfatos y Turbidez sobre la potabilidad.
* **Detección de Outliers:** Identificación de registros que exceden el umbral crítico de $|z| > 3$.

### 3. Dataset - HTTP :
Expone los siguientes *endpoints* para la integración con sistemas externos:

| Método | Endpoint | Descripción |
| :--- | :--- | :--- |
| `GET` | `/ds/health` | Estado del sistema y verificación de caché. |
| `POST` | `/ds/reload` | Dispara una recarga completa del dataset. |
| `GET` | `/ds/stats` | Retorna estadísticas descriptivas detalladas. |
| `GET` | `/ds/corr` | Matriz de correlación respecto al objetivo (Potability). |
| `GET` | `/ds/hists` | Datos de distribución para visualización de histogramas. |
| `GET` | `/ds/sample` | Retorna una muestra aleatoria (parámetros: `n`, `potability`). |

## 🚀 Instalación y Configuración

1.  **Requisitos:** Tener instalado Node-RED y el nodo `node-red-node-csv` (incluido por defecto).
2.  **Importación:** Copie el código JSON proporcionado en este repositorio e impórtelo mediante la opción *Import* en el menú de Node-RED.
3.  **Configuración del Path:** * Localice el nodo de inyección "Set CSV path (Windows)".
    * Actualice la ruta absoluta del archivo `water_potability_cleaned.csv` según su sistema local.
    * Ejecute la inyección para inicializar el contexto global.

## 📝 Notas Técnicas :
El sistema emplea funciones de JavaScript nativas para el cálculo de cuantiles y coeficientes de correlación, evitando la sobrecarga de dependencias externas y garantizando una ejecución ligera en entornos industriales o de *edge computing*.







# Node-RED — Clear and Practical Explanation

## What is Node-RED?
Node-RED is a **low-code, flow-based development tool** built on **Node.js**. It allows you to wire together devices, APIs, databases, and services using a **visual programming interface** instead of writing large amounts of code.

It is widely used in:
- IoT systems
- Automation workflows
- API orchestration
- Data processing pipelines
- Rapid prototyping

---

## Core Concept: Flows
In Node-RED, logic is implemented through **flows**.

A **flow** is a sequence of connected **nodes** that:
1. Receive input
2. Process data
3. Produce output

Each flow runs **event-driven**, meaning it reacts when data arrives.

---

## Nodes Explained
Nodes are the building blocks of Node-RED.

### 1. Input Nodes
They **start** a flow by injecting data.
Examples:
- `inject` (manual or scheduled trigger)
- `http in`
- `mqtt in`
- `websocket in`

---

### 2. Processing Nodes
They **transform or analyze** data.
Examples:
- `function` (custom JavaScript logic)
- `change` (set, move, delete properties)
- `switch` (conditional routing)
- `template`

---

### 3. Output Nodes
They **send data** to an external system or user.
Examples:
- `debug`
- `http response`
- `mqtt out`
- `file`
- `database nodes`

---

## Messages (`msg`)
Node-RED works by passing **messages** between nodes.

A message is a JavaScript object:
```js
msg = {
  payload: "...",
  topic: "...",
  _msgid: "..."
}
