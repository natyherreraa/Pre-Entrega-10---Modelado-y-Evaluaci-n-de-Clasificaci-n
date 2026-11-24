# Pre-entrega 10 · Predicción de cancelación de pólizas

El objetivo es identificar pólizas de automotores con riesgo de cancelación para orientar acciones de retención. Se comparan dos modelos de clasificación binaria, regresión logística y Random Forest, sobre una cartera de 2024 con 168.133 registros. La variable objetivo es `policy_status`: se codifica la póliza activa (`A`) como 0 y la cancelada (`C`) como 1. Las cancelaciones representan el 12,36 % de la cartera.

## Ejecución local 

1. Clonar el repositorio público de GitHub a entorno local. 
2. Verificar que el dataset esté en `data/raw/dataset.csv`. El notebook se encuentra en la raíz del repositorio y carga el archivo desde esa ruta relativa.

Link de descarga del dataset: https://drive.google.com/file/d/1H2_T2xycHODlPM7hkc1sbxweu6Q7ZE23/view?usp=sharing

3. Desde la carpeta principal del repositorio, creá y activá el entorno virtual

   ```bash
  python -m pip install -r requirements.txt
   ```

4. Con el entorno virtual activo, instalá las dependencias del proyecto.

   ```bash
   python -m pip install -r requirements.txt
   ```

5. Abrí `modelado_metricas.ipynb` en VS Code con la extensión Jupyter. En **Seleccionar kernel**, elegí el entorno `.venv` y hacé clic en **Ejecutar todo**. El notebook cargará el dataset desde `data/raw/dataset.csv` y mostrará las métricas y las matrices de confusión de ambos modelos.

## Metodología

Se reserva el 80 % de las pólizas para entrenamiento y el 20 % para prueba. La división es estratificada por estado de póliza y utiliza `random_state=42` para que pueda reproducirse. El preprocesamiento se ajusta con los datos de entrenamiento: se imputan los faltantes, se estandarizan las variables numéricas y se codifican las categóricas. Se excluyen el identificador del asegurado y las variables de siniestros, costos incurridos y exposición que podrían incorporar información posterior a la predicción.

## Resultados

Las métricas se calcularon sobre 33.627 pólizas de prueba, con la cancelación como clase positiva y un umbral de clasificación de 0,50.

| Modelo | Accuracy | Precision | Recall | F1-score |
|---|---:|---:|---:|---:|
| Regresión logística | 0,6694 | 0,2217 | 0,6671 | 0,3328 |
| Random Forest | 0,8342 | 0,3841 | 0,5660 | 0,4576 |

Para una campaña que busca detectar la mayor cantidad posible de bajas, se prioriza el **Recall**. La regresión logística identificó 2.773 de las 4.157 cancelaciones reales. Las 1.384 restantes fueron falsos negativos: si el equipo de retención contactara únicamente a las pólizas señaladas, esas quedarían fuera de la campaña.

La regresión logística también generó 9.734 falsas alertas. Si la capacidad de contacto fuera limitada, Random Forest ofrecería mayor precisión: aproximadamente 38 de cada 100 alertas correspondieron a cancelaciones reales, frente a 22 de cada 100 con regresión logística.
