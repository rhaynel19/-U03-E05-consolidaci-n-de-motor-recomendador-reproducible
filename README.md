# INF-8239 · Unidad 03 · Motor Recomendador Reproducible

Proyecto académico correspondiente a la **Unidad 03 – Algoritmos y Características Generativas** de la asignatura **INF-8239 · Ciencia de Datos II**, enfocado en el desarrollo, evaluación y reproducción de un sistema de recomendación híbrido.

**Autor académico:** Edwin Ramón José Nolasco
**Estudiante:** Fraimel Trinidad Medina
**Institución:** Universidad Autónoma de Santo Domingo (UASD)

---

## 📌 Descripción del proyecto

Este proyecto consolida los experimentos desarrollados en los laboratorios **LAB08** y **LAB09** para construir un motor recomendador reproducible.

El sistema integra diferentes estrategias de recomendación:

* **Popularidad suavizada** mediante promedio bayesiano.
* **Filtrado basado en contenido** utilizando TF-IDF y similitud coseno.
* **Filtrado colaborativo** mediante factorización de matrices.
* **Sistema híbrido** mediante combinación ponderada de señales colaborativas y de contenido.
* **Gestión de usuarios nuevos (Cold Start)** mediante un mecanismo de respaldo basado en popularidad.
* **Evaluación temporal** para reducir el riesgo de *data leakage*.
* **Métricas de precisión, ranking y cobertura** para analizar el comportamiento del sistema.

El objetivo no es únicamente generar recomendaciones, sino proporcionar un proceso **ejecutable, auditable y reproducible**.

---

## 🗂️ Dataset

El desarrollo utiliza como conjunto de evidencia final **MovieLens Latest Small (`ml-latest-small`)**.

El dataset contiene:

* **100,836** valoraciones.
* **9,742** películas.
* **610** usuarios.
* Densidad aproximada de la matriz de interacciones: **1.70 %**.
* Valoración media global: **3.50 estrellas**.

Los datos originales no se incluyen directamente en el repositorio. Se utiliza un script de descarga y auditoría para facilitar la reproducción del experimento.

La integridad del archivo descargado puede verificarse mediante SHA-256.

> **Nota:** El dataset de muestra utilizado durante las etapas iniciales sirve para validar la ejecución del código. La evidencia experimental final se obtiene utilizando MovieLens.

---

## ⚙️ Requisitos

* Python **3.12**
* `uv`
* Git
* Dependencias definidas en `pyproject.toml`

El proyecto utiliza `uv` para gestionar el entorno virtual y las dependencias de forma reproducible.

---

## 🚀 Instalación y configuración

Clonar el repositorio:

```bash
git clone <URL_DEL_REPOSITORIO>
cd <NOMBRE_DEL_REPOSITORIO>
```

Instalar Python 3.12:

```bash
uv python install 3.12
```

Sincronizar el entorno:

```bash
uv sync
```

Ejecutar las pruebas:

```bash
uv run pytest -q
```

---

## 📥 Descarga y auditoría de datos

Descargar el dataset:

```bash
uv run python scripts/download_data.py
```

Auditar los datos:

```bash
uv run python scripts/audit_data.py
```

La auditoría permite verificar aspectos como:

* Integridad del archivo.
* Número de usuarios.
* Número de películas.
* Número de valoraciones.
* Estructura de los datos.
* Estadísticas básicas.
* Consistencia del dataset utilizado.

---

# 🧪 Laboratorios

## LAB08 · Recomendación basada en contenido

Ejecutar:

```bash
uv run python scripts/lab08_content.py
```

Este laboratorio implementa un recomendador basado en contenido utilizando:

**Géneros → TF-IDF → Similitud coseno → Ranking de recomendaciones**

El modelo utiliza las características de las películas para identificar elementos similares a las preferencias históricas del usuario.

---

## LAB09 · Filtrado colaborativo y sistema híbrido

Ejecutar:

```bash
uv run python scripts/lab09_hybrid.py --factors 20 --epochs 12 --alpha 0.75
```

Parámetros utilizados en la evidencia final:

* **Factores latentes:** 20
* **Épocas:** 12
* **Alpha:** 0.75

El parámetro `alpha` controla la contribución del componente colaborativo dentro del sistema híbrido:

```text
hybrid_score =
    alpha × normalized
    + (1 - alpha) × content_score
```

Por ejemplo:

```text
alpha = 0.75
```

otorga mayor peso a la señal colaborativa.

También puede evaluarse una configuración con mayor peso del contenido:

```bash
uv run python scripts/lab09_hybrid.py --factors 20 --epochs 12 --alpha 0.25
```

---

# 🖥️ Aplicación interactiva

Para ejecutar la interfaz desarrollada con Streamlit:

```bash
uv run streamlit run app/streamlit_app.py
```

La aplicación permite interactuar con el motor recomendador y visualizar los resultados generados por el sistema.

---

# 📊 Resultados principales

Las configuraciones evaluadas sobre el conjunto de prueba temporal produjeron los siguientes resultados:

| Configuración |   RMSE | HitRate@10 | Cobertura |  Tiempo |
| ------------- | -----: | ---------: | --------: | ------: |
| α = 0.75      | 1.0264 |     3.75 % |    7.75 % | 25.32 s |
| α = 0.25      | 1.0264 |     3.24 % |   10.65 % | 25.72 s |

### Interpretación

La configuración **α = 0.75** presenta un mejor **HitRate@10**, indicando un mayor desempeño en el criterio de ranking utilizado.

Por otro lado, **α = 0.25** obtiene una mayor **cobertura del catálogo**, favoreciendo una distribución más amplia de las recomendaciones.

Esto evidencia un compromiso entre **relevancia del ranking y cobertura/diversidad del catálogo**.

El RMSE permanece en **1.0264** en ambas configuraciones debido a que la métrica se deriva de la estimación base de la factorización utilizada en la evaluación.

---

# 🧊 Cold Start

Los usuarios sin historial suficiente de interacciones representan un problema conocido como **Cold Start**.

Para estos casos, el sistema utiliza un mecanismo de respaldo (*fallback*) basado en la popularidad global calculada a partir del conjunto de entrenamiento.

La evidencia generada se encuentra en:

```text
reports/cold_start_fallback.csv
```

Esta estrategia garantiza que el sistema pueda producir una recomendación incluso cuando no existe suficiente información histórica del usuario.

---

# 🌱 Green AI y eficiencia computacional

El proyecto fue diseñado considerando principios de eficiencia computacional.

Los experimentos se ejecutaron en un entorno local utilizando:

* Python 3.12
* `uv`
* 20 factores latentes
* 12 épocas de entrenamiento

Los tiempos observados se encuentran aproximadamente entre **25.3 y 25.7 segundos**.

La ejecución local permite realizar los experimentos sin depender de infraestructura especializada de alto consumo computacional.

Los tiempos de ejecución deben interpretarse como una medida de rendimiento computacional y no como una medición directa del consumo energético o de las emisiones de carbono.

---

# 🧪 Pruebas

Las pruebas automatizadas pueden ejecutarse mediante:

```bash
uv run pytest -q
```

La suite permite verificar el comportamiento de los principales componentes del proyecto y detectar regresiones durante el desarrollo.

---

# 📁 Estructura del proyecto

```text
.
├── app/
│   └── streamlit_app.py
│
├── data/
│   └── ...
│
├── reports/
│   ├── hybrid_metrics.json
│   └── cold_start_fallback.csv
│
├── scripts/
│   ├── audit_data.py
│   ├── download_data.py
│   ├── lab08_content.py
│   └── lab09_hybrid.py
│
├── src/
│   └── inf8239_u03/
│       ├── config.py
│       ├── data.py
│       ├── metrics.py
│       └── recommenders.py
│
├── tests/
│   └── ...
│
├── pyproject.toml
└── README.md
```

---

# 🔁 Reproducibilidad

Para reproducir la evidencia experimental desde un entorno limpio:

```bash
uv python install 3.12
uv sync
uv run pytest -q
uv run python scripts/download_data.py
uv run python scripts/audit_data.py
uv run python scripts/lab08_content.py
uv run python scripts/lab09_hybrid.py --factors 20 --epochs 12 --alpha 0.75
```

Para ejecutar la segunda configuración experimental:

```bash
uv run python scripts/lab09_hybrid.py --factors 20 --epochs 12 --alpha 0.25
```

La reproducción depende de mantener consistentes la versión de Python, las dependencias, los parámetros experimentales y el procedimiento de partición temporal utilizado.

---

# ⚖️ Uso responsable de las recomendaciones

Las recomendaciones generadas por este sistema **no deben interpretarse como verdades objetivas ni como decisiones definitivas**.

Cada resultado debe analizarse considerando:

1. Los datos utilizados para entrenar el modelo.
2. El historial disponible del usuario.
3. Los candidatos considerados.
4. La puntuación asignada por el modelo.
5. Las métricas de evaluación.
6. El mecanismo de *fallback* utilizado.
7. Las limitaciones del dataset y del modelo.

Un sistema recomendador aprende patrones a partir de datos históricos; por tanto, sus resultados pueden reflejar sesgos, limitaciones de cobertura y características propias del conjunto de entrenamiento.

---

# 🤖 Declaración de uso de Inteligencia Artificial

Durante el desarrollo del proyecto se utilizaron herramientas de asistencia basadas en Inteligencia Artificial para apoyar determinadas tareas de programación, resolución de problemas técnicos, estructuración de resultados y redacción de documentación.

La asistencia algorítmica no sustituye la responsabilidad del estudiante sobre el diseño experimental, la ejecución del código, la validación de los resultados y las conclusiones presentadas.

**El estudiante es responsable de los resultados finales incluidos en este repositorio.**

---

# 👨‍🎓 Autor

**Fraimel Trinidad Medina**

Proyecto académico desarrollado para:

**INF-8239 · Ciencia de Datos II**
**Unidad 03 · Algoritmos y Características Generativas**
**Universidad Autónoma de Santo Domingo (UASD)**

**Profesor:** Edwin Ramón José Nolasco

