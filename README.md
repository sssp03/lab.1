# LABORATORIO 1
Como primer paso para la realización de la guía, descargamos desde PhysioNet una señal de EMG, obteniendo los archivos en formato `.dat` y `.hea`. Posteriormente, utilizamos Python para calcular lo solicitado y generar las gráficas correspondientes, empleando el compilador de Google Colab.  

En este entorno, redactamos el código necesario para visualizar los datos estadísticos, siguiendo las indicaciones de la guía. Se requería que la representación de los datos se realizara de dos maneras diferentes: primero, implementando las fórmulas desde cero, y luego, utilizando las funciones predefinidas de Python.  

Con esta base, procedimos a calcular los siguientes estadísticos de la señal: media, desviación estándar, coeficiente de variación, histogramas y función de probabilidad. Después de completar esta parte, nos enfocamos en investigar la relación señal-ruido (SNR), ya que era necesario contaminar la señal con ruido gaussiano, impulsivo y tipo artefacto, para luego medir el SNR de cada uno.  

Para llevar a cabo todo esto, desarrollamos la programación correspondiente en Python, la cual explicaremos a continuación.
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
```python
record = wfdb.rdrecord(file_path)
fs = record.fs  # Frecuencia de muestreo
signal = record.p_signal[:, 0] * 1000  # Convertir a mV
time = np.arange(len(signal)) / fs  # Eje de tiempo
```
- `record = wfdb.rdrecord(file_path)`: Este comando se utiliza para leer un archivo de señal biomédica desde PhysioNet. El parámetro file_path corresponde a la ruta del archivo que en nuestro caso era de Drive. El resultado, record, va contener toda la información sobre la señal, como los datos de la señal, la frecuencia de muestreo, y más.
- `fs = record.fs`: Aquí, record.fs obtiene la frecuencia de muestreo de la señal, es decir, cuántos puntos de datos se capturan por segundo. Esta información es importante ya que convierte los índices de los datos en unidad de tiempo.
- `signal = record.p_signal[:, 0] * 1000`: record.p_signal es un array que contiene las señales en bruto.,El [:, 0] selecciona todos los valores de la primera columna (ya que los archivos pueden contener más de una señal) y despues multiplicamos por 1000 (* 1000) para convertir la señal a milivoltios (mV), si la señal original está en voltios (V).
- `time = np.arange(len(signal)) / fs`: Esta linea crea el eje de tiempo para la señal, np.arange(len(signal)) genera números enteros desde 0 hasta el número de muestras en la señal (len(signal)).Luego, lo dividimos por la frecuencia de muestreo fs para convertir esos índices en tiempo (en segundos). Esto nos da el tiempo correspondiente a cada punto de la señal, lo que nos permitirá graficar la señal frente al tiempo de manera adecuada.

Despues de esto se va a graficar la señal fisiologica orginal mediante matplotlib permitiendo visualizar de manera clara, para esto se va necesitar las siguientes lineas de codigo:
```python
plt.figure(figsize=(10, 4))
plt.plot(time, signal, label="Señal Original")
plt.xlabel("Tiempo (s)")
plt.ylabel("Amplitud (mV)")
plt.title("Señal Biomédica")
plt.legend()
plt.grid()
plt.show()
```
![image](https://github.com/user-attachments/assets/84cb0797-a76c-443a-9757-1163c8d334b9)

- `plt.figure(figsize=(10, 4))`:Crea una nueva figura para el gráfico, con un tamaño de 10x4 pulgadas. Esto ayuda a ajustar la visualización del gráfico a un tamaño adecuado.
- `plt.plot(time, signal, label="Señal Original")`:Dibuja la señal en el gráfico, utilizando el eje de tiempo time y los valores de la señal signal. El parámetro label="Señal Original" sirve para asignar un nombre a la línea que se mostrará en la leyenda del gráfico.
- `plt.xlabel("Tiempo (s)")`:Establece la etiqueta del eje x (horizontal), en nuestro caso el tiempo en segundos.
- `plt.ylabel("Amplitud (mV)")`:Establece la etiqueta del eje y (vertical), indicando que el eje representa la amplitud de la señal en milivoltios (mV).
- plt.title("Señal Biomédica")`: Esyte comando nos permite asignarle el nombre a nuestra grafica.
- `plt.legend()`:Muestra la leyenda del gráfico, que en este caso incluirá la etiqueta "Señal Original" para identificar la línea que representa la señal.
- `plt.grid()`: Esto Añade una cuadrícula al gráfico para facilitar la visualización de los valores.
- `plt.show()`: Nos va a mostrar la grafica en la interfaz o pantalla.
Posteriormente, se va a calcular los datos estadisticos manualmente que son la media, desviaciòn estandar y coeficiente de variaciòn, para esto se utilizo el siguiente codigo:
```python
def calcular_media_manual(signal):
    suma = 0
    for x in signal:
        suma += x
    return suma / len(signal)

def calcular_desviacion_manual(signal, media):
    return (sum((x - media) ** 2 for x in signal) / len(signal)) ** 0.5

def calcular_coef_var_manual(desviacion, media):
    return (desviacion / media) * 100 if media != 0 else 0
```
- `calcular_media_manual(signal)`:Esto permite calcula la media (promedio) de la señal,La variable suma acumula todos los valores de esta.Luego, se divide la suma total entre el número de elementos de la señal (len(signal)) para obtener la media.
- `calcular_desviacion_manual(signal, media)`: Por medio de esta función se calcula la desviación estándar de la señal,primero se calcula la diferencia al cuadrado de cada valor de la señal con respecto a la media,luego se suman esos valores y se divide entre el número total de elementos de la señal y finalmente se toma la raíz cuadrada del resultado para obtener la desviación estándar.
- `calcular_coef_var_manual(desviacion, media)`:Esto va calcular el coeficiente de variación, que es la relación entre la desviación estándar y la media, multiplicada por 100 para expresarlo en porcentaje, si la media es distinta de cero, se realiza el cálculo. En caso contrario, se retorna 0, evitando la división por cero.
  
Como en la guía también se solicitaba calcular los datos anteriores utilizando las funciones predefinidas de Python, se optó por hacer uso de las funciones de **NumPy** de manera más eficiente y compacta. Esto permitió realizar los cálculos de la media, la desviación estándar y el coeficiente de variación de forma rápida, aprovechando las optimizaciones de la librería sin tener que implementar las fórmulas manualmente.
```python
media_numpy = np.mean(signal)
desviacion_numpy = np.std(signal)
coef_var_numpy = (desviacion_numpy / media_numpy) * 100
```

En esta parte del código se empieza a graficar el histograma de la señal
``` python
plt.figure(figsize=(10, 4))
plt.hist(signal, bins=30, alpha=0.7, color='blue', edgecolor='black')
plt.title('Histograma de la Señal')
plt.xlabel('Amplitud (mV)')
plt.ylabel('Frecuencia')
plt.grid(True)
plt.show()
```
![image](https://github.com/user-attachments/assets/9dbf861a-6087-424d-a60a-5dc88a6eab7b)

donde se elige el tamaño de la gráfica, se nombran sus respectivos ejes, se da el color y lo más importante se establece el parámetro de los intervalos del histograma, es decir, la cantidad de barras, que fueron específicamente 30 para obtener una representación clara de la distribución sin perder demasiada información. 

Luego se procede a graficar la función de densidad de probabilidad (estimación)
``` python
plt.figure(figsize=(10, 4))
plt.hist(signal, bins=30, density=True, alpha=0.6, color='g', edgecolor='black')
plt.title('Función de Probabilidad (Densidad de la Señal)')
plt.xlabel('Amplitud (mV)')
plt.ylabel('Probabilidad')
plt.grid(True)
plt.show()
```
![image](https://github.com/user-attachments/assets/b7d3fc0e-84f4-488a-a1ea-965c0691799b)

Se hace prácticamente lo del paso anterior, pero con la diferencia de que, en lugar de mostrar la frecuencia, se muestra la probabilidad (densidad de la señal) con la función específica “density”

Continuamos con la representación de la campana de Gauss
``` python
plt.figure(figsize=(10, 5))
plt.hist(signal, bins=30, density=True, alpha=0.6, color='b', edgecolor='black', label="Histograma de la señal")
x = np.linspace(min(signal), max(signal), 100)
pdf = norm.pdf(x, media_numpy, desviacion_numpy)
plt.plot(x, pdf, 'r-', linewidth=2, label="Campana de Gauss")
plt.title("Distribución de la Señal con Campana de Gauss")
plt.xlabel("Amplitud (mV)")
plt.ylabel("Densidad de Probabilidad")
plt.legend()
plt.grid(True)
plt.show()
```
![image](https://github.com/user-attachments/assets/448be64d-07fb-4248-a821-073696cd17b6)
- `np.linspace():` Genera una secuencia de números igualmente espaciados entre un valor mínimo y máximo. En este caso, genera 100 valores que cubren el rango de amplitudes de la señal para evaluar la campana de Gauss.
- `norm.pdf():` Calcula la función de densidad de probabilidad (PDF) de una distribución normal ya que esta función toma como entrada el rango de valores (de x), y la media y desviación estándar de los datos. Devuelve los valores correspondientes a la altura de la campana en esos puntos.
- `plt.plot():` Dibuja la campana de Gauss sobre el gráfico, usando los valores de x y pdf calculados previamente. Esta función conecta los puntos con una curva continua.

11 
    ``` python
    def calcular_snr(original, ruidosa):
    potencia_original = np.mean(original ** 2)
    potencia_ruido = np.mean((original - ruidosa) ** 2)
    return 10 * np.log10(potencia_original / potencia_ruido)
    ```
Paso 11: Cálculo de la Relación Señal-Ruido (SNR)
Descripción:
Este paso calcula la Relación Señal-Ruido (SNR), que mide la calidad de la señal al comparar la potencia de la señal útil con la potencia del ruido añadido. Un SNR más alto indica que la señal es más clara en comparación con el ruido.

Función utilizada:

np.mean():Calcula el valor promedio de un arreglo. Se usa aquí para calcular la potencia de la señal y el ruido, que es el promedio del cuadrado de los valores.
10 * np.log10():Esta fórmula convierte la relación de potencias en decibelios (dB), lo cual es una escala logarítmica común para medir el SNR.

En esta sección del código se añade el ruido solicitado a la señal

a) Ruido Gaussiano

``` python
ruido_gaussiano = np.random.normal(0, 5, len(signal))
senal_gaussiana = signal + ruido_gaussiano
snr_gaussiano = calcular_snr(signal, senal_gaussiana)
print(f"SNR con ruido gaussiano: {snr_gaussiano} dB")
```

b) Ruido de Impulso

``` python
ruido_impulso = np.random.choice([0, 50, -50], size=len(signal), p=[0.95, 0.025, 0.025])
senal_impulso = signal + ruido_impulso
snr_impulso = calcular_snr(signal, senal_impulso)
print(f"SNR con ruido de impulso: {snr_impulso} dB")
```

c) Ruido de artefacto

``` python
ruido_artefacto = 20 * np.sign(np.sin(2 * np.pi * 1 * time))
senal_artefacto = signal + ruido_artefacto
snr_artefacto = calcular_snr(signal, senal_artefacto)
print(f"SNR con ruido de artefacto: {snr_artefacto} dB")
```
- np.random.normal():Genera números aleatorios con distribución normal. Se usa para crear el ruido gaussiano, con una media de 0 y una desviación estándar de 5.
- np.random.choice():Selecciona valores aleatorios de un conjunto dado, en este caso, para simular el ruido de impulso. Elige entre 0, +50 y -50 mV con probabilidades específicas.
- np.sign(np.sin()):Crea una onda cuadrada a partir de la función seno, que se usa para simular el ruido de artefacto. La función np.sign() convierte los valores del seno en 1 o -1.

Aquí se puede evidenciar las señales contaminadas con los tres tipos ruidos diferentes

``` python
fig, axs = plt.subplots(3, 1, figsize=(10, 8))
axs[0].plot(time, senal_gaussiana, label="Ruido Gaussiano", color='r')
axs[1].plot(time, senal_impulso, label="Ruido de Impulso", color='g')
axs[2].plot(time, senal_artefacto, label="Ruido de Artefacto", color='purple')
plt.tight_layout()
plt.show()
```
  ![image](https://github.com/user-attachments/assets/c24cb84e-b35c-488a-a5f8-e8cff499bd09)
  ![image](https://github.com/user-attachments/assets/c8e8fced-9b45-46b3-85f2-de3e8dde04c6)
  ![image](https://github.com/user-attachments/assets/53bc4814-f939-4fd5-b391-49d037b303ca)

- plt.subplots(): Crea múltiples subgráficos (subplots) dentro de una misma figura. En este caso, se crean tres gráficos para mostrar las señales con ruido gaussiano, ruido de impulso y ruido de artefacto en diferentes colores.

- plt.tight_layout():Ajusta el espaciado entre los gráficos para que no se solapen.

 

