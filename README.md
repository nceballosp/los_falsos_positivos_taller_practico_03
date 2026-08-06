# Taller Práctico 03 — Los Falsos Positivos

**Curso:** Fundamentos en Ciencia de Datos — Maestría en Ciencia de Datos y Analítica, EAFIT  
**Fecha límite de entrega:** Viernes 7 de Agosto  
**Fecha de entrega real:** [/08/2026]  

**Integrantes del equipo:**

| Nombre Completo                  | Cédula         |
| -------------------------------- | -------------- |
| Eduardo Andres Piñeros Manjarres | 1067841294     |
| Juan Jose Vasquez Gomez          | 1017924089     |
| Natalia Ceballos Posada          | 1034987681     |

## Descripción del taller

Este repositorio contiene el desarrollo del Taller Práctico 03, cuyo propósito es aplicar técnicas de ciencia de datos para identificar patrones, anomalías y posibles falsos positivos en información energética y geo-agrónoma.

El trabajo integra análisis exploratorio, limpieza de datos, visualización, análisis de redes, pruebas de causalidad temporal y modelación predictiva. El caso de negocio central se presenta como **La Falla del Nodo 214**: cuando el Precio Spot (`Ener_2`) supera un umbral crítico, el flujo hacia determinados nodos puede interrumpirse y coincidir con una anomalía térmica en las coordenadas asociadas.

## Objetivos

- Explorar y preparar los datasets de energía y agro.
- Identificar comportamientos anómalos y relaciones relevantes entre variables.
- Analizar la estructura de la red energética mediante métricas de centralidad.
- Evaluar relaciones temporales entre el factor de potencia y el voltaje.
- Localizar sensores agro-ambientales prioritarios para inversión hídrica.
- Comparar modelos ARIMAX para explicar y predecir la demanda energética.
- Comunicar los resultados mediante gráficos, conclusiones técnicas y recomendaciones de negocio.

## Organización del repositorio

```text
.
├── data/                         # Datos utilizados por el análisis
├── guias/                        # Material de orientación del taller
├── informe/figuras/              # Figuras de apoyo para P1, P2 y P3
│   ├── p1.png
│   ├── p2.png
│   └── p3.png
├── informe/                     # Informe técnico en LaTeX y PDF compilado
├── notebooks/
│   └── taller_practico_03.ipynb  # Desarrollo principal del taller
├── requirements.txt              # Dependencias de Python
└── README.md                     # Documentación del proyecto
```

## Flujo de trabajo realizado

El notebook está organizado como un flujo analítico reproducible:

1. **Carga y revisión de datos:** se inspeccionan dimensiones, tipos, valores faltantes, duplicados y rangos de las variables.
2. **Preparación:** se normalizan nombres de columnas, se convierten variables a formatos adecuados y se preparan los conjuntos de energía y agro.
3. **Análisis exploratorio:** se estudian distribuciones, relaciones entre variables y posibles observaciones atípicas mediante tablas y visualizaciones.
4. **Análisis de red:** se construye un grafo dirigido con las conexiones entre `Source_Node` y `Target_Node` y se calculan métricas de centralidad.
5. **Problema de negocio del nodo 214:** se desarrollan las preguntas P1, P2 y P3 en bloques de código independientes, con una figura de soporte para cada pregunta.

## Resultados principales del punto 4

### P1. Causalidad y redes

Se aplicó la prueba de causalidad de Granger entre el Factor de Potencia (`Ener_10`) y el Voltaje (`Ener_9`) para rezagos de 1 a 10.

- En la dirección **Potencia → Voltaje**, el menor valor-p fue aproximadamente `0.0193` en el rezago 4, por debajo del nivel de significancia `0.05`.
- En la dirección **Voltaje → Potencia**, el menor valor-p fue aproximadamente `0.1125`, por lo que no se encontró evidencia estadística suficiente en esa dirección.
- La red contiene 70 nodos y 865 aristas.
- El nodo 214 presenta una centralidad de grado aproximada de `0.2319`.
- La Betweenness Centrality resultó igual a cero para todos los nodos en la topología observada, debido a que la red está compuesta principalmente por conexiones directas origen-destino.

La interpretación operativa es que una perturbación en un nodo con alta conectividad puede afectar varios enlaces, modificar la distribución de carga y producir desviaciones de voltaje en nodos vecinos. Para medir intermediación de forma más representativa sería conveniente disponer de una red de transmisión completa o construir el grafo por intervalos operativos.

La evidencia gráfica se encuentra en [`informe/figuras/p1.png`](informe/figuras/p1.png).

### P2. Optimización geo-agrónoma

Se aplicó un filtro de mediana móvil de ventana 5 a las coordenadas GPS para reducir ruido espacial. Después se definió una zona prioritaria combinando:

- NDVI menor o igual al percentil 25: `NDVI <= 0.8619`.
- Varianza del viento `Agro_10` mayor o igual al percentil 75: `varianza >= 6.0998`.

La intersección identificó 132 sensores de 2.000 observaciones, equivalentes al 6.6% del conjunto. Estos puntos representan zonas con menor vigor vegetal y mayor exposición a variabilidad del viento.

La recomendación es iniciar un piloto de infraestructura hídrica focalizado en estas zonas, con riego presurizado y sectorizado, sensores de humedad de suelo, regulación local de caudal y medidas de protección contra viento cuando sean agronómicamente viables. Antes de escalar la inversión deben validarse pendiente, textura del suelo, disponibilidad de agua y caudal de diseño.

La evidencia gráfica se encuentra en [`informe/figuras/p2.png`](informe/figuras/p2.png).

### P3. Analítica predictiva

Se compararon dos modelos SARIMAX para la Demanda (`Ener_1`), manteniendo la misma muestra de análisis:

| Modelo | Orden ARIMA | AIC |
| --- | --- | ---: |
| Temperatura | `(2,1,0)` | 7167.695 |
| Temperatura + importancia del nodo | `(2,1,0)` | 7169.439 |

El modelo que incluye la centralidad del nodo obtiene un AIC 1.744 puntos mayor. Como un AIC menor es preferible, la inclusión de la importancia del nodo no mejora el modelo bajo esta especificación. Este resultado es válido para el periodo, las variables y la forma de construir la centralidad utilizados en el taller.

La evidencia gráfica se encuentra en [`informe/figuras/p3.png`](informe/figuras/p3.png).

## Entregables

- **Notebook:** [`notebooks/taller_practico_03.ipynb`](notebooks/taller_practico_03.ipynb), con el desarrollo, código, resultados y visualizaciones.
- **Figuras:** [`informe/figuras/`](informe/figuras/), con los gráficos de soporte de las preguntas P1, P2 y P3.
- **Informe técnico:** carpeta [`informes/`](informes/), con el documento LaTeX y su versión compilada en PDF.

## Ejecución

Se recomienda utilizar el entorno virtual del proyecto e instalar las dependencias declaradas en `requirements.txt`:

```bash
python -m venv .venv

# Windows PowerShell
.venv\\Scripts\\Activate.ps1

pip install -r requirements.txt
jupyter notebook notebooks/taller_practico_03.ipynb
```

El notebook define una semilla aleatoria (`RANDOM_SEED = 7`) y contiene lógica de carga para evitar depender exclusivamente del orden de ejecución de celdas. Para reproducir los resultados, se debe ejecutar el notebook de arriba hacia abajo y verificar que las rutas de los datos correspondan a la estructura del repositorio.

## Consideraciones metodológicas

- La prueba de Granger evalúa precedencia temporal y capacidad predictiva incremental; no demuestra causalidad física por sí sola.
- La centralidad depende de cómo se construye el grafo. Una red bipartita o de conexiones directas puede producir valores de Betweenness iguales a cero.
- La relación entre NDVI, pendiente y varianza del viento debe validarse con información topográfica y agronómica adicional antes de tomar decisiones de inversión.
- La comparación de AIC solo es válida entre modelos ajustados sobre la misma muestra y con una definición consistente de la variable objetivo y de las exógenas.

## Equipo

El trabajo fue realizado por Eduardo Andres Piñeros Manjarres, Juan Jose Vasquez Gomez y Natalia Ceballos Posada, como parte del curso Fundamentos en Ciencia de Datos de la Maestría en Ciencia de Datos y Analítica de EAFIT.
