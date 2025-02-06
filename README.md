# LABORATORIO 1
Como primer paso para la realización de la guía, descargamos desde PhysioNet una señal de EMG, obteniendo los archivos en formato `.dat` y `.hea`. Posteriormente, utilizamos Python para poder calcular lo solicitado y las graficas respectivas , empleando el compilador de Google Colab.  

En este entorno, redactamos el código necesario para visualizar los datos estadísticos, siguiendo las indicaciones de la guía. Estas requerían que la representación se realizara de dos maneras diferentes: primero, implementando las fórmulas desde cero y, luego, utilizando las funciones predefinidas de Python.  

Con esta base, procedimos a calcular los siguientes estadísticos de la señal: media, desviación estándar, coeficiente de variación, histogramas y función de probabilidad. Para realizar estos cálculos, desarrollamos la programación correspondiente en Python, la cual explicaremos a continuación.
# EXPLICACION DEL CODIGO USADO 
Lo primero que debemos hacer es importar las librerías necesarias, ya que estas son fundamentales para el correcto funcionamiento de nuestro código.
```python
!pip install wfdb
import wfdb
import os
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import norm  # Para la función de distribución normal
from google.colab import drive
```
En el compilador que estamos utilizando, cada vez que vayamos a ejecutar el código, es necesario instalar la librería `wfdb` utilizando el siguiente comando:

```python
!pip install wfdb
```
Esta librería es fundamental, ya que permite leer y procesar archivos de señales fisiológicas, como los obtenidos de PhysioNet.  

Luego, importamos las siguientes librerías necesarias para el análisis y procesamiento de los datos:  

- `import os`: Permite manejar archivos del sistema.  
- `import numpy as np`: Esencial para realizar los cálculos matemáticos requeridos.  
- `import matplotlib.pyplot as plt`: Se utiliza para la generación de gráficos.  
- `from scipy.stats import norm`: Facilita el cálculo y representación de la campana de Gauss.  
- `from google.colab import drive`: Se emplea para conectar Google Drive con Google Colab, ya que nuestros archivos habían sido subidos previamente a Drive.

Despues de esto se utlizo `drive.mount('/content/drive')` que este permite acceder a los archivos que tenemos almacenados en Drive.
```python
directory = "/content/drive/My Drive/lab 1 ps"
file_name = "emg_neuropathy"
file_path = os.path.join(directory, file_name)
```
Esta parte del código permite que Google Colab acceda a Google Drive y busque el archivo con el nombre que le asignaste previamente al subirlo.Después de esto, se carga la señal biomédica utilizando el siguiente comando de Python: 
-`record = wfdb.rdrecord(file_path)':
fs = record.fs  # Frecuencia de muestreo
signal = record.p_signal[:, 0] * 1000  # Convertir a mV
time = np.arange(len(signal)) / fs  # Eje de tiempo
record = wfdb.rdrecord(file_path): Este comando se utiliza para leer un archivo de señal biomédica desde PhysioNet. El parámetro file_path corresponde a la ruta del archivo que en nuestro caso era de Drive. El resultado, record, va contener toda la información sobre la señal, como los datos de la señal, la frecuencia de muestreo, y más.
fs = record.fs:
Aquí, record.fs obtiene la frecuencia de muestreo de la señal, es decir, cuántos puntos de datos se capturan por segundo. Esta información es esencial para procesar correctamente la señal, ya que nos permite convertir los índices de los datos en unidades de tiempo.
