# Bienvenidos al laboratorio de Machine learning

## Convenciones del laboratorio

Para poder mejorar la reproducibilidad es necesario acordar convenciones para algunos procesos y formatos de tal forma que lo que construyamos pueda integrarse con trabajos previos de manera más rápida. Algunos lineamientos propuestos son:

## Para modificar un repo o agregar alguna característica

La mayoría de nuestros repos tiene una rama de desarrollo en caso contrario es importante crearla. En caso de querer agregar una característica a algún repo podríamos seguir estos pasos:

**1. Clona el repositorio y crea tu rama:** clona el repocon `git clone` y crear una Rama de Característica Empieza desde la rama dev, ya que es tu rama de integración de características para el desarrollo:
```bash
git clone
git checkout dev # Nos situamos en la rama de desarrollo
git pull origin dev # Asegúrate de tener los últimos cambios
git checkout -b feature/nombre-de-la-característica # Crea y cambia a la nueva rama
```

**2. Desarrolla la Característica:** Realiza todos tus cambios en esta rama feature/nombre-de-la-característica. Haz commits de manera atómica y con mensajes descriptivos:

```bash
git add . # Agregamos los archivos sobre los que hayamos hecho cambios en este ejemplo se usa . para todos los archivos pero se puede especificar sólo un path
git commit -m "Añadir cambios específicos"
```
> [!IMPORTANT]
> Manten tu rama de característica actualizada con los cambios que otros puedan estar integrando en dev para evitar conflictos más adelante usando los comandos siguientes de manera regular.

```bash
git checkout dev #regreessamos a la rama de desarrollo
git pull origin dev # Descargamos los últimos cambios en dev
git checkout feature/nombre-de-la-característica # Cambiamos de nuevo a nuestra rama
git merge dev # integramos los cambios de dev en nuestra rama de característica. También podemos usar git rebase dev 
```
si la característica está asociada a un issue puede hacer un pull request o merge request

**3. Integra la Característica en Dev:** Después de que la característica ha sido revisada y aprobada, puedes integrarla en dev:

```bash
git checkout dev
git merge feature/nombre-de-la-característica # o realiza el merge a través de una interfaz de usuario de Git. cuando hacemos esto git crea un commit de fusión y se puede mandar con un push
git push origin dev # integramos nuestros cambios a la rama de dev
```
**4. Despliegue en un Entorno de Pruebas:** En algunos casos puedes querer hacer un despliegue en un entorno de pruebas antes de integrarlo al main. Con la característica ya en dev, puedes desplegar en un entorno de pruebas para realizar pruebas de aceptación de usuario y otras validaciones.

**5. Integración a Main y Despliegue a Producción:** Una vez que la característica está probada y es estable en dev y en el entorno de pruebas, puedes moverla a main. La mayoría de nuestros repos que están en producción siguen un flujo para que al empujar a main se actualice la aplicación en productivo.

```bash
git checkout main
git pull origin main
git merge dev # o realiza un merge específico de la rama de característica si es la política
git push origin main
```
Es recomendable etiquetar tu código con una nueva versión:
```bash
git tag -a v1.0.0 -m "Nueva versión con característica X"
git push origin --tags
```
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

## Respecto a los ambientes
Es preferible usar Pyenv para manejar ambientes y versiones de python de manera aislada. Puedes ver los detalles de su instalación en windows [aquí](https://pypi.org/project/pyenv-win/)
o aún mejor con devcontainers en vscode. Ver el confluence de [mind](https://mind.indra.es/pages/viewpage.action?pageId=731817556)

## Respecto al uso de contenedores

Primero es importante notar que docker no es el único proveedor de contenedores. Sin embargo es el más utilizado, para usar docker primero podemos instalarlo en función de nuestro SO https://docs.docker.com/engine/install/
una vez instalado podemos correr ´docker ps´  en nuestra línea de comandos para ver que está bien isntalado

## Respecto al uso de credenciales
Las credenciales que se vayana a usar en el proyecto no deben subirse a ningún repositorio ya sea hardcodeadas o en archivos independientes. Es preferible que se usen como variables de ambiente para usar en local y se especifiquen en el readme del proyecto o como secrets en alguno de los proveedores de nube en caso de que sea necesario. Si se opta por .env es importante dejar en el repo un .env.example para que otro desarrollador pueda hacer su propio .env y se se agregen el .env al .gitignore del repositorio.

Para hacer push y pull desde el servidor de bitbucket de plaiground_mx es necesario usar la VPN con doble autenticación y usar credenciales de usuario. Sin emargo es posible guardar las credenciales con `git config --global credential.helper store`

## Respecto al trabajo en notebooks
para usar magic recuerda que ! se usa para todos los usuarios de la máquina y % es sólo para el usuario que está ejecutando
Esto es importante en cuadernos no gestionados por nosotros en local porque podemos afectar dependencias de terceros como en sagemaker. En ese supuesto es preferible usar `%pip install seaborn` a menos que queramos cambiar las dependencias de los demás usuarios.
## Respecto al naming de columnas
Se recomienda usar minúsculas y evitar los caracteres especiales y se puede usar la instrucción .clean_names()

## Respecto a la estructura de las carpetas en github
[Disponible en Project Organization](https://github.com/AI-ML-Lab/atom)

## Respecto a las licencias de modelos

Las licencias pueden cambiar de modelo a modelo y tener mayores o menores restricciones. Una buena guía para abordarlas es la de [RAIL](https://www.licenses.ai/blog/2022/8/18/naming-convention-of-responsible-ai-licenses)
![RAIL diagram](https://images.squarespace-cdn.com/content/v1/5c2a6d5c45776e85d1482a7e/381a69a4-28d9-4b6a-be5c-516330e7d8b9/diagram.png?format=1500w)

## Respecto a Scrum
Los tiempos asignados a cada tarea se basan en los siguientes puntajes:
- 0.5: *Un café*
- 1: Una hora
- 2: Medio día
- 3: Un día
- 5: Media semana
- 8: Una semana
- 

## Sobre la documentación en repositorios

Es posible encontrar la plantilla de los aceleradores en el [readme](https://github.com/AI-ML-Lab/resources/blob/main/Readme.md) del repo de resources. Sin embargo podemos resumir la estructura con las siguientes secciones:
- Encabezado con banner y badges
- Benchmark
	- Comparación cualitativa
  	- Métricas existentes 
	- Comparativa cuantitativa
- Seguridad
	- De las dependencias
	- De la arquitectura
- Licencias
- Integraciones
	- Recomendaciónes de implementación on premise
	- Recomendaciones de implementación en nube
	- Otras plataformas y servicios
- Costos
	- Productos lowcode en nube
	- Productos necesarios para su uso con instancias
- Referencias
- Tiempo

Para la configuración de los badges usamos [shield](https://shields.io/docs) para que sean dinámicos. 
para la consulta de las versiones usamos el documento [properties.json](https://github.com/AI-ML-Lab/resources/blob/main/properties.json)
es necesario cambiar el nombre del repositorio y el campo que se requiere haciendo una query con sintaxis [JSONPath](https://jsonpath.com/) como en este ejemplo:

![image](https://github.com/AI-ML-Lab/.github/assets/26263145/73493667-9bd9-4c59-85b9-fb8d9ce8d60c)

En este ejemplo queremos acceder en el nodo *'repo'* al subelemento con el nombre del repositorio *'ac_generacion-imagen'* y devolver el atributo *'version'* del primer elemento de la lista *[0]* por lo que la sintaxis sería ```$.repo.ac_generacion-imagen[0].version```

## Respecto a la seguridad

Todos los proyectos, pruebas de concepto y demos deben pasar por una revisión de seguridad por el responsable del proyecto en su versión estable. Los elementos de seguridad a incorporar, revisar y mantener dependerán del proyecto y sus necesidades específicas sin embargo enunciamos algunos rubros que deben considerarse y en caso de no incorporar elementos de seguridad en alguno de los rubros dejar declarada la razón por la cuál no se incorporó

![image](https://github.com/AI-ML-Lab/.github/assets/26263145/05f4cb9a-1231-4261-8fcb-65870813633c)


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
![rtd](https://github.com/AI-ML-Lab/resources/blob/main/images/Captura%20de%20pantalla%202023-09-15%201919291.png?raw=true)



