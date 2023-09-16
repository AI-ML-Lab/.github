# Bienvenidos al laboratorio de Machine learning

## Convenciones del laboratorio

Para poder mejorar la reproducibilidad es necesario acordar convenciones para algunos procesos y formatos de tal forma que lo que construyamos pueda integrarse con trabajos previos de manera más rápida. Algunos lineamientos propuestos pueden ser:
## Respecto a la organización de los repositorios de información
Los archivos que mande cliente se mantienen en su ubicación original o en el repositorio central destinado a cliente en caso de que lo envíe por distintos medios diferenciando por carpetas el contexto y los extractos y dentro de los extractos los extractos hechos on demand y los que pueden ser obtenidos por un stream
Se generará una carpeta fuera del repositorio de cliente con el resultado de la información procesada con un readme que haga referencia a la información contenida, un resumen de las transformaciones o procesos de limpieza aplicados y una referencia al recurso (código o jupyter) con el que fueron procesados que estará disponibe en github un ejemplo sería una estructura como esta:
- Nombre del proyecto
	- MLLab
		- Archivo de contexto 1 (presentación de ventas) -f
		- Archivo de contexto 2 (planeación de tiempos de proyecto) -f
		- Entregables - d
		- Proceso -d
			- Datos - d
				- Readme -f
				- Tablas EDA auxiliares- d
					- Extracto 1 -f
				- Datos procesados -d
					- Extracto 1 -f
				- Input de modelos -d
					- Extracto 1 -f
				- Tablas intermedias - d
					- Extracto 1 -f
				- Output de modelos - d
					- Extracto 1 -f
				- Tablas para Sandbox - d (tablas consumibles para BI)
					- Extracto 1 -f
				-
				-
			- Plots - d
				- waop_ae_ts-availability.png
	- Cliente
		- Archivo de contexto 1 (Plantilla de contactos)
		- Archivo de contexto 2 (Accesos, etc)
		- Endpoint
			- Datos crudos
## Respecto a los nombres de los archivos
Los nombres de los archivos procesados llevarán una nomenclatura uniforme de al menos 3 elementos comunes en minúsculas separado por guiones bajos.
	- Los elementos comunes de la nomenclatura serían:
		- ***Identificador del proyecto:*** 4 letras. Ejemplo: waop, seco, ppsi
		- ***Identificador de proceso:*** 2 letras Ejemplo:
			- ae: análisis exploratorio
			- dp: datos procesados
			- im: imput de modelos
			- in: datos intermedios
			- om: output de modelos
			- mo: modelos
			- sa: sandbox
		- ***Identificador de contenido:*** metrics-availability, metrics-cpu, pgm-servers
		- ***Periodo:*** yymm-yymm (opcional)
	- Un ejemplo de nombre de archivo sería entonces:
		- waop_dp_metrics-availability_2104-2204.csv
		
## Respecto al de almacenamiento
Es preferible almacenar los datos procesados por Minsait en formatos comprimidos. Se recomienda el uso de parquets para subirlos a la nube y el uso de csv para Sharepoint a menos que el archivo exceda el millón de filas o los 100 MB
Los archivos procesados deberán ser subidos a los buckets de s3 dispuestos para cada proyecto en una estructura idéntica a la de los repositorios de información. A menos que el cliente haya provisto accesos a su propia infraestructura de cloud.
	- Para subir los parquets a los buckets de s3. Se puede recurrir a la librería boto3 autenticándose con su clave secreta y su key id. Por ejemplo para ver el contenido de un bucket puede usar:
``` python
!pip install boto3
import boto3
######################### Authentication start
access_key_id = 'credenciales'
secret_access_key = 'access_key'

s3 = boto3.resource('s3', aws_access_key_id = access_key_id, 
		    aws_secret_access_key= secret_access_key)

######################### Authentication end
PROJECT_NAME = 'proyectosailab'
bucket = s3.Bucket(PROJECT_NAME)
for obj in bucket.objects.all():
print(obj.key)
```
Para descargar la información del proyecto una vez autenticado puede usar este comando:

```python
PROJECT_NAME = 'proyectosailab'
OBJECT_NAME = 'se_procesada.zip'
FILE_NAME = './Data.zip'

s3.Object(proyecto, OBJECT_NAME).download_file(FILE_NAME)
```
Para cargar información puede usar

```python
PROJECT_NAME = 'waop'
FILE_NAME = 'waop_availability.parquet.gzip'
OBJECT_NAME = 'waop_availability.parquet.gzip'

s3.meta.client.upload_file(FILE_NAME, PROJECT_NAME, OBJECT_NAME)
```

## Respecto al trabajo en notebooks
para usar magic recuerda que ! se usa para todos los usuarios de la máquina y % es sólo para el usuario que está ejecutando
 
## Respecto al naming de columnas
Se recomienda usar minúsculas y evitar los caracteres especiales y se puede usar la instrucción .clean_names()

## Respecto a la estructura de las carpetas en github
[Disponible en Project Organization](https://github.com/AI-ML-Lab/atom)

## Respecto a Scrum
Los tiempos asignados a cada tarea se basan en los siguientes puntajes:
- 0.5: *Un café*
- 1: Una hora
- 2: Medio día
- 3: Un día
- 5: Media semana
- 8: Una semana
## Documentación funcional con Sphinx

Para hacer documentación formato read the docs con sphinx se recomienda instalar sphinx y el tema de read the docs con los siguientes comandos:

```bash
pip install sphinx
pip install sphinx-rtd-theme
```

una vez instalado ir al proyecto en donde está su módulo y ejecutar lo siguiente: 
```bash
cd docs
sphinx-quickstart
```
en las opciones siguientes puede usarse las opciones por default y rellenar los campos

Lo importante es una vez terminado modificar el archivo conf.py y agregar lo siguiente en las primeras lineas:

```python
import os
import sys

sys.path.insert(0, os.path.abspath('..'))
```

sustituir la lista vacía de extensiones por lo siguiente para poder autodocumentar a partir de los docstrings

```python
extensions = ["sphinx.ext.autodoc", "sphinx.ext.viewcode", "sphinx.ext.napoleon"]
```
y cambiar el valor de la variable html_theme como sigue:

```python
html_theme = 'sphinx_rtd_theme'
```
guardamos el archivo y en línea de comando ejecutamos aún en el directorio docs:

```bash
sphinx-apidoc -o . ..
```
por último agregamos el recién creado modules al archivo index.rst como a continuación:

```
Welcome to vectordb api's documentation!
========================================

.. toctree::
   :maxdepth: 2
   :caption: Contents:

   modules



Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`
```
lo guardamos yejecutamos:

```bash
make html
```

y listo en la ruta docs/build/html encontrarás la documentación como a continuación:
![rtd](images/Captura de pantalla 2023-09-15 1919291.png)



