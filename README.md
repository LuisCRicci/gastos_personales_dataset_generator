# 💰 GENERADOR DE DATASET SINTÉTICO: GASTOS PERSONALES BASADO EN RMV E INFLACIÓN

## 📋 Resumen del Proyecto

Este proyecto tiene como objetivo generar un **dataset sintético y realista** de transacciones financieras (ingresos y gastos) para un individuo, utilizando como base el **historial y la proyección de la Remuneración Mínima Vital (RMV)** en Perú y la **inflación anual** proyectada.

El dataset simula el comportamiento financiero diario, semanal, mensual y anual de una persona que percibe ingresos cercanos al salario mínimo, incluyendo factores cruciales como las **gratificaciones** de julio y diciembre y la distribución típica del gasto peruano.

El dataset generado es ideal para prácticas y demostraciones de **Ciencia de Datos, Machine Learning (detección de anomalías, categorización) o análisis financiero** en un entorno sin datos sensibles reales.

---

## 🚀 Estructura de Archivos

| Archivo | Descripción |
| :--- | :--- |
| `generator.ipynb` | **Notebook principal.** Contiene todo el código para la carga de datos, limpieza, proyección, configuración de parámetros y la generación del dataset sintético. |
| `historial_salario_minimo.csv` | Datos históricos de la RMV en Perú (mensual). Fuente: BCR. |
| `inflacion_Peru_BCR.csv` | Datos históricos de la Tasa de Inflación (IPC) anual. Fuente: BCR. |
| `proyeccion_salario_minimo.csv` | Output del Paso 1. Proyección de la RMV hasta el año final de simulación (e.g., 2035). |
| `proyeccion_inflacion_BCR_lineal_v3.csv` | Output del Paso 2. Proyección de la Inflación anual. |
| **`dataset_gastos_personales_simulado.csv`** | **Output final del proyecto.** Contiene todas las transacciones sintéticas generadas (fecha, monto, tipo, descripción, categoría) en el periodo simulado. |
| `proyeccion_salario_minimo.png` | Gráfico de la proyección del Salario Mínimo. |
| `balance_mensual_simulado.png` | Gráfico del balance neto (Ingresos vs. Gastos) a lo largo del periodo simulado. |

---

## ⚙️ Metodología y Pasos de Ejecución

El proceso se divide en 4 pasos principales, ejecutables secuencialmente en el notebook `generator.ipynb`:

### 1. Extracción y Limpieza de Datos Históricos
* Se cargan los archivos CSV de historial de RMV e Inflación.
* Se realiza la limpieza de formatos de fecha y la normalización de valores para preparar los datos para la modelización.

### 2. Modelización y Proyección (RMV e Inflación)
* **RMV:** Se aplica una **Regresión Polinomial** al historial de la RMV para proyectar su valor hasta el año final de la simulación (e.g., 2035).
* **Inflación:** Se utiliza una **Regresión Lineal Simple** para proyectar la tasa de inflación anual.
* Los resultados se guardan en `proyeccion_salario_minimo.csv` y `proyeccion_inflacion_BCR_lineal_v3.csv`.

### 3. Configuración de Parámetros de Simulación
* Se define la `ESTRUCTURA_GASTO` (porcentajes por categoría como Alimentación, Vivienda, Transporte).
* Se realiza el crucial *merge* de las proyecciones de RMV (mensual promediada a anual) e Inflación (anual) para obtener un *DataFrame* de proyecciones anuales (`df_proyecciones`).

### 4. Generación del Dataset Sintético (Transacciones)
Se itera mes a mes a lo largo del periodo de simulación, utilizando la función `generar_dataset_financiero_mensual()`:

* **Ingresos:** Se generan ingresos basados en la RMV proyectada para ese año y mes, incluyendo un evento de **Gratificación** (salario extra) en **Julio** y **Diciembre**.
* **Gastos:** Se simulan las transacciones de gasto con alta densidad, siguiendo la `ESTRUCTURA_GASTO` y la periodicidad definida:
    * **Diario/Semanal:** Transporte, Alimentación (Fuera/Hogar).
    * **Mensual:** Vivienda, Servicios, Suscripciones.
    * **Esporádico:** Salud, Vestido, Ocio.
* Se aplica una variación aleatoria controlada (`numpy.random.uniform`) al monto de cada transacción para aumentar el realismo.
  


---

## 📈 Visualización de Resultados

El proyecto genera tres visualizaciones clave:

### 1. Proyección del Salario Mínimo hasta el año 2035

![alt text](proyeccion_salario_minimo.png)


### 2. Proyección de Inflación hasta el año 2035

![alt text](proyeccion_inflacion_BCR_lineal.png)

### 3. Balance Mensual Simulado

*Muestra el saldo (Ingreso - Gasto) mes a mes, destacando picos de ingreso en los meses de gratificación (Julio y Diciembre).*

![alt text](balance_mensual_simulado.png)

## 💻 Requisitos y Dependencias

Para ejecutar el notebook `generator.ipynb`, necesitarás las siguientes librerías de Python:

```bash
pandas
numpy
matplotlib
scikit-learn (sklearn)
random
datetime
os

```

## 🕰️ Configuracion del Periodo de Tiempo
Puedes configurar el periodo de tiempo para generar el dataset sintetico modificando las siguinetes lineas: 

```python
# =========================================================================
# 3. EXTRACCIÓN Y CONFIGURACIÓN DE PARÁMETROS DE SIMULACIÓN
# =========================================================================

# --- 3.1. Definición de Parámetros de Simulación ---

# Parámetro 1: Rango de tiempo para generar el dataset (Ajustar por el usuario)
# Año maximo para la generacion de datos es 2034 
# Año minimo es 1998
# >>> AJUSTAR ESTA LÍNEA (AÑO DE INICIO)
ANIO_INICIO_SIMULACION = 2025  
# >>> AJUSTAR ESTA LÍNEA (AÑO FINAL)
ANIO_FIN_SIMULACION = 2027 # Por ejemplo, un periodo futuro
# Nota: La simulación se generará por cada mes dentro de este rango de años.

# Parámetro 2: Configuración del Ingreso
PORCENTAJE_INGRESO_EXTRA_PROMEDIO = 0.50 # 50% del Salario Mínimo en promedio por Horas Extra / Freelance
DIAS_PAGO_SALARIO = 30 # Día del mes en que se recibe el salario (ej. el día 30)
```


![alt text](image.png)


## ⚙️ Obciones de Mejora y Personalización

#### * Personalizar scrapin_precios_candymarket.ipynb
 puedes modificar el notebook ***scrapin_precios_candymarket.ipynb*** para extraer los productos y precios de otra fuente y adptarlos dentro de del notebook ***generator.ipynb*** en estas secciones de codigo: 

```python
# ==========================================================
# 3.2.1 Mapeo de Archivos CSV de Productos a Categorías
# ==========================================================
PRODUCTOS_CSV_MAP = {
    'Alimentacion (Hogar)': [
        "productos/abarrotes.csv",
        "productos/carnes_aderezadas_y_especerias.csv",
        "productos/carnes.csv",
        "productos/embutidos.csv",
        "productos/frutas_y_verduras.csv",
        "productos/lacteos_y_huevos.csv",
        # 'panaderia_y_pasteleria.csv' se omite ya que tiene un TRATAMIENTO ESPECIAL (compra diaria, monto fijo)
    ],
    'Salud': ["productos/farmacia.csv"],
    'Mobiliario y Enseres': [
        "productos/ferreteria.csv",
        "productos/limpieza.csv",
        "productos/menaje.csv",
        "productos/tocador_y_aseo_personal.csv"
    ],
    'Otros Gastos y Diversos': [
        "productos/golosinas.csv",
        "productos/jugos_y_nectares.csv"
    ],
    'Alimentacion (Fuera)': ["productos/patio_de_comidas.csv"]
}

......

# --- Inicio del PASO 3: Generación del Dataset Sintético de Transacciones ---

# ==========================================================
# 1. Preparación de Data de Alimentación (Hogar)
# ==========================================================

print("Cargando data de productos de hogar...")

# 💡 NOTA: Asegúrate de que los archivos 'abarrotes.csv', 'carnes.csv',
# y 'frutas_y_verduras.csv' estén en el mismo directorio.

df_abarrotes = pd.read_csv('productos/abarrotes.csv')
df_carnes = pd.read_csv('productos/carnes.csv')
df_frutas_y_verduras = pd.read_csv('productos/frutas_y_verduras.csv')


# Unir y limpiar los productos relevantes para Alimentación (Hogar)
productos_alimentacion_hogar = pd.concat([df_abarrotes, df_carnes, df_frutas_y_verduras], ignore_index=True)
productos_alimentacion_hogar = productos_alimentacion_hogar[['nombre_producto', 'unidad_medida', 'precio_por_unidad']].drop_duplicates()
# Eliminar productos no aptos para compra diaria (ej. sacos grandes de 10kg, etc.)
productos_alimentacion_hogar = productos_alimentacion_hogar[
    ~productos_alimentacion_hogar['nombre_producto'].str.contains('10 KILOS|10KG|Detergente', case=False, na=False)
].reset_index(drop=True)

# Lista para almacenar los nuevos gastos de hogar generados
nuevos_gastos_hogar = []

print(f"Productos listos para simulación: {len(productos_alimentacion_hogar)}")

...

# ==============================================================================
# PASO 3.5: Preparación y Consolidación de Data de Productos (CSV)
# ==============================================================================
print("Cargando y pre-procesando data de productos de hogar...")

try:
    # 1. Cargar archivos de productos
    df_abarrotes = pd.read_csv('productos/abarrotes.csv')
    df_carnes = pd.read_csv('productos/carnes.csv')
    df_frutas_y_verduras = pd.read_csv('productos/frutas_y_verduras.csv')
    
    # 2. Consolidar productos de Alimentación (Hogar)
    productos_hogar_raw = pd.concat([df_abarrotes, df_carnes, df_frutas_y_verduras], ignore_index=True)

    # 3. RENOMBRAR las columnas para hacerlas consistentes
    productos_hogar_clean = productos_hogar_raw.rename(columns={
        'nombre_producto': 'descripcion_producto',
        'precio_por_unidad': 'precio_unitario'
    })

    # 4. Filtrar y seleccionar las columnas finales, asegurando 'unidad_medida'
    productos_hogar_clean = productos_hogar_clean[[
        'descripcion_producto', 
        'unidad_medida', 
        'precio_unitario'
    ]].drop_duplicates().reset_index(drop=True)

    # 5. Crear el diccionario PRODUCTOS_DF (Asumiendo que 'Alimentacion (Hogar)' es la clave)
    # 💡 Nota: Si tienes más categorías (ej. 'Salud'), debes añadirlas aquí también con la misma estructura.
    PRODUCTOS_DF['Alimentacion (Hogar)'] = productos_hogar_clean
    
    print(f"✅ Se consolidaron {len(productos_hogar_clean)} productos para Alimentación (Hogar). Columnas estandarizadas.")
    
except Exception as e:
    print(f"❌ Error al pre-procesar los CSVs. Asegúrate de que los archivos estén en el directorio y las columnas sean correctas: {e}")


```

#### * Personalizar el porcentaje de distribucion de gasto

 puedes modificar el notebook ***generator.ipynb***  para refeljar la distribucion de gasto de tu localidad modificando las asignaciones decimales dentro de cada categoria, en el caso actual se usa el 99 % del ingreso para solventar los gastos dejando un 1% para ahorro.

```python

ESTRUCTURA_GASTO = {
    'Alimentacion (Hogar)': {
        'porcentaje': 0.36, 
        'periodicidad': 'Diario', 
        'keywords': ["supermercado", "market", "metro", "wong", "tottus", "plaza vea", "mass", "bodega", "mercado", "panaderia", "leche", "frutas", "verduras", "carniceria", "pollo", "carne", "arroz", "aceite"]
    },
    'Vivienda y Servicios': {
        'porcentaje': 0.25, 
        'periodicidad': 'Mensual', 
        'keywords': ["alquiler", "renta", "luz del sur", "enel", "sedapal", "gas natural", "internet y telefono", "claro", "movistar", "entel", "mantenimiento", "Xturbo"]
    },
    'Transporte y Comunicaciones': {
        'porcentaje': 0.15, 
        'periodicidad': 'Diario', 
        'keywords': ["gasolina", "grifo Repsol", "estacionamiento", "bus", "Metropolitano", "urbano" ]
    },
    'Alimentacion (Fuera)': {
        'porcentaje': 0.08, 
        'periodicidad': 'Semanal', 
        'keywords': ["almuerzo restaurant", "comida rapida", "menu", "cafe", "starbucks", "bembos", "pollo a la brasa", "chifa", "picanteria", "anticuchos"]
    },
    'Vestido y Calzado': {
        'porcentaje': 0.05, 
        'periodicidad': 'Esporádico', 
        'keywords': ["ropa", "zapatos", "tienda", "saga", "ripley", "falabella", "oechsle", "cosmetico", "perfume", "accesorio" , "maquillaje"]
    },
    'Salud': {
        'porcentaje': 0.02, 
        'periodicidad': 'Variable', 
        'keywords': ["farmacia Inkafarma", "farmacia Mifarma", "medicina", "consulta medica", "vitaminas", "analisis laboratorio"]
    },
    'Educacion y Ocio': {
        'porcentaje': 0.03, 
        'periodicidad': 'Mensual', 
        'keywords': ["netflix", "spotify", "hbo", "cine", "concierto", "curso online", "libro", "gimnasio", "suscripcion"]
    },
    'Mobiliario y Enseres': {
        'porcentaje': 0.02, 
        'periodicidad': 'Mensual', 
        'keywords': ["articulos de limpieza", "ferreteria", "reparacion", "mueble", "decoracion"]
    },
    'Otros Gastos y Diversos': {
        'porcentaje': 0.03, 
        'periodicidad': 'Variable', 
        'keywords': ["regalo", "donacion", "cuidado personal", "peluqueria", "barberia", "transferencia a familiar"]
    }
}

```
Ademas tambien puedes modificar las proyecciones de Inflación y  Remuneracion Mínima Vital (RMV) ingresando los datos de tu localidad en los CSVs ***historial_salario_minimo.csv***, ***inflacion_Peru_BCR*** 






## 📎 Notas Finales

Se recomienda ejecutar el notebook de principio a fin para garantizar coherencia en las proyecciones.

Proyecto orientado a análisis financiero sintético y educación en ciencia de datos.