# LABORATORIO 1
Como primer paso para la realización de la guía, descargamos desde PhysioNet una señal de EMG, obteniendo los archivos en formato `.dat` y `.hea`. Posteriormente, utilizamos Python para poder calcular lo solicitado y las graficas respectivas , empleando el compilador de Google Colab.  

En este entorno, redactamos el código necesario para visualizar los datos estadísticos, siguiendo las indicaciones de la guía. Estas requerían que la representación se realizara de dos maneras diferentes: primero, implementando las fórmulas desde cero y, luego, utilizando las funciones predefinidas de Python.  

Con esta base, procedimos a calcular los siguientes estadísticos de la señal: media, desviación estándar, coeficiente de variación, histogramas y función de probabilidad. Para realizar estos cálculos, desarrollamos la programación correspondiente en Python, la cual explicaremos a continuación.
# EXPLICACION DEL CODIGO USADO 
Lo primero que debemos hacer es importar las librerías necesarias, ya que estas son fundamentales para el correcto funcionamiento de nuestro código.
'''python
!pip install wfdb
import wfdb
import os
import numpy as np
import matplotlib.pyplot as plt
from scipy.stats import norm  # Para la función de distribución normal
from google.colab import drive
