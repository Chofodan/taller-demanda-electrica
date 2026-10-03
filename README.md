# Predicción de demanda eléctrica con Python y ML

Taller del **Python Exposition Day 2026** · 3 de octubre · Guatemala

[![Abrir en Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/TU-USUARIO/taller-demanda-electrica/blob/main/taller_demanda_ALUMNO.ipynb)

Construimos, de principio a fin, un modelo que pronostica la demanda eléctrica
**con 24 horas de anticipación**: hoy a las 10 de la mañana queremos saber cuánta
energía se va a necesitar mañana, hora por hora.

Ese detalle —las 24 horas de anticipación— define todo lo demás. Es la diferencia
entre un ejercicio de juguete y un modelo que sirve.

---

## Empezar

1. Haz clic en el botón de Colab de arriba.
2. `Archivo → Guardar una copia en Drive`.
3. Corre la primera celda y sigue el taller.

No hay que instalar nada. Donde diga `# TODO` te toca escribir código.

| Notebook | Para qué |
|---|---|
| [`taller_demanda_ALUMNO.ipynb`](taller_demanda_ALUMNO.ipynb) | El del taller, con 6 ejercicios |
| [`taller_demanda_RESUELTO.ipynb`](taller_demanda_RESUELTO.ipynb) | Todo resuelto y ejecutado, por si te atoras |

## Requisitos

- Python y pandas básicos. Nada más.
- Navegador y cuenta de Google.
- **No** hace falta experiencia previa en series de tiempo.

## Lo que vamos a ver

| Bloque | Tema |
|---|---|
| 1 | Anatomía de la demanda: tres estacionalidades y un termómetro |
| 2 | El horizonte, los baselines y las métricas |
| 3 | Ingeniería de variables: lags, ventanas móviles, feriados, clima |
| 4 | XGBoost y validación temporal |
| 5 | Evaluación e interpretación con SHAP |
| 6 | De notebook a producción |

## La idea central

> En series de tiempo el modelo es la parte fácil.
> Lo difícil es construir las variables y validar sin hacerse trampa.

Como pronosticamos a 24 horas, **`lag_1` no existe**: el dato de la hora anterior a
la que predices todavía no ha ocurrido. Casi todos los tutoriales de forecasting lo
usan, reportan un error precioso y entregan un modelo que se cae el primer día en
producción. Aquí no.

## Los datos

Por defecto el notebook **genera** tres años de demanda horaria sintética pero
realista: triple estacionalidad, efecto no lineal del clima, feriados de Guatemala
y ruido con memoria. Es determinista, así que todos obtienen exactamente los mismos
números, y corre sin internet.

¿Tienes datos reales? Pon la URL o la ruta de tu CSV en `URL_DATOS`, en la tercera
celda. El cargador no asume el formato: prueba separadores (`,` `;` tab) y
decimales, detecta las columnas por nombre, admite fecha y hora en columnas
separadas —incluida la convención 1–24 del AMM—, ordena, quita duplicados y avisa
de los huecos.

## Los números que salen

Con los datos sintéticos, sobre el periodo de prueba:

| Modelo | MAPE | Mejora |
|---|---|---|
| Naïve · ayer `y(t-24)` | 5.51 % | — |
| Naïve · semana pasada `y(t-168)` | 3.59 % | +35 % |
| XGBoost sin clima | 2.28 % | +36 % |
| XGBoost con clima | **2.07 %** | +9 % |

Y los dos números de la trampa: **0.55 %** si suavizas la serie con una ventana
centrada antes de partir los datos, **1.64 %** con un split aleatorio. El primero
es espectacular y falso. El segundo es el peligroso, porque no se nota.

## Para seguir

- Cambia el corte entre entrenamiento y prueba, y mira si las conclusiones aguantan.
- Prueba regresión cuantílica para tener bandas en lugar de un solo número.
- Compara contra LightGBM y contra un modelo lineal con las mismas variables.
  Te va a sorprender lo cerca que queda el lineal.
- Aplica el mismo flujo a otra serie: ventas, tráfico, consumo de agua. Cambia el
  dominio, no el método.

---

Hecho para la comunidad Python de Guatemala. Si algo no corre, abre un *issue*.
