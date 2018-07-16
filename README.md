Starterkit de proyecto en Django 2.0 para desplegar en Heroku (PaaS)
===

[Heroku](https://www.heroku.com/) es una plataforma como servicio (PaaS) donde podemos desplegar rápidamente nuestras  aplicaciones, donde el tedioso proceso de configuración de servidores es reducido de buena manera. El balanceo de carga, escalabilidad, loggin, seguridad, integración continua, despliegue continuo, rollback, integración con el repositorio de código, etc. es manejado por Heroku, con el objetivo de que el desarrollador solo se enfoque en la etapa de código, con el mínimo esfuerzo destinado a la gestión de servidores. 

En el siguiente ejemplo se presenta un proyecto básico de despliegue que presenta las siguientes características:

- Python 3.6.2
- Uso de la herramienta [virtualenv](https://virtualenv.pypa.io/en/stable/) para aislar entornos de Python
- Se usa el [Framework Django 2.0](https://www.djangoproject.com/)
- Entornos de despliegue separados: local, producción y testing.
- Configurado para ser desplegado en Heroku (Usando Github) con los siguientes addons:
    - Heroku Postgres (SQL)
    - Papertrail (Gestión de registros (logs))
    - Sentry (Monitoreo de errores)

En este tutorial también se explica el uso de Apps Reviews usando la herramienta Pipelines de Heroku, así como continous delivery.

Paso 1: Creación y uso de un entorno virtual
-----
Después de haber clonado el repositorio remoto, en linux (para su instalación en windows seguir en [enlace](https://simpleisbetterthancomplex.com/series/2017/09/04/a-complete-beginners-guide-to-django-part-1.html)) creamos el entorno virtual, usando **virtualenv**, para lo cual, procedemos primero a instalarlo globalmente usando pip.

`enrique@ex-machina:~$ sudo pip install virtualenv`

Seguidamente, creamos un entorno virtual con la versión de Python objetivo, en este caso 3.6:

`enrique@ex-machina:~$ virtualenv --python=python3.6 venv`

Activamos nuestro entorno virtual:

```
enrique@ex-machina:~$ source venv/bin/activate
(venv) enrique@ex-machina:~$
```
Paso 2: Cargar variables de entorno
----

Debemos mantener los ambientes de ejecución separados: local, heroku o testing; según sea el caso, para lo cual para nuestro entorno de desarrollo local, debemos crear archivo de variables de entorno del proyecto. Tal archivo podemos llamarlo como deseemos, sin embargo, por motivos específicos a este proyecto procederemos a llamarlo **.env**, el cual tendrá los siguientes parámetros:

`export DJANGO_SETTINGS_MODULE=starterkit.settings.local`

Este archivo es importante, ya que en el van parámetros particulares a cada desarrollador/proyecto, como llaves de acceso a servicios/apis, entorno de ejecución, etc. 

Una vez creado tal archivo, procedemos a "cargar" su contenido en las variables del sistema, usando el siguiente comando: 

`(venv) enrique@ex-machina:~$ source .env`

Considerar que las variable especificadas, se cargará localmente. Para cargar tales variables al entorno de producción en Heroku, podemos usar el archivo de configuración `app.json`, en la sección "env":
```
...
  "env": {
    "DJANGO_SETTINGS_MODULE": {
      "value": "starterkit.settings.heroku"
    },
  },
...
```

Paso 3: Ejecutar el proyecto localmente
----
Una vez con el entorno virtual aislado, tenemos que instalar las dependecias usando el administrador  de paquetes de python [pip](https://pypi.org/project/pip/). Siguiendo las buenas prácticas de desarrollo, éstos paquetes están listados en el archivo `requeriments/local.py`, por lo que, ejecutamos:

`(venv) enrique@ex-machina:~$ pip install -r requeriments/local`

Ahora, procedemos a [migrar](https://docs.djangoproject.com/en/2.0/topics/migrations/) la base de datos:

```
(venv) enrique@ex-machina:~$ cd starterkit
(venv) enrique@ex-machina:~/starterkit$ python manage.py migrate
```

Después, procedemos a ejecutar el servidor de aplicaciones local:

```
(venv) enrique@ex-machina:~/starterkit$ python manage.py runserver
Performing system checks...
System check identified no issues (0 silenced).
July 16, 2018 - 22:25:32
Django version 2.0.5, using settings 'starterkit.settings.local'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Podemos probar la correcta ejecución ingresando a la dirección: [http://127.0.0.1:8000/](http://127.0.0.1:8000/)

Paso 4: Desplegar el proyecto a la nube (Heroku)
---

Existen determinadas fases cuando se despliega la aplicación en Heroku: build, release y  deploy. Para cada una se puede especificar comandos extras o algún parámetro en especial en el archivo `app.json`.

Para desplegar nuestra aplicación, creamos un [dyno](https://www.heroku.com/dynos) en nuestro panel principal de Heroku.

![image](https://user-images.githubusercontent.com/5711590/42787243-4784dfda-891f-11e8-9b4e-899a56719871.png)

Ingresamos los datos solicitados, una vez dentro de la pestaña _deploy_ en la configuración, establecemos como _deployment method_  a Github, seguidamente, lo conectamos a nuestro repositorio remoto de github.
![image](https://user-images.githubusercontent.com/5711590/42787403-fdc1eb8a-891f-11e8-813b-6d488557917c.png)

Presionamos deploy branch, el cual se debería ejecutar sin errores.

Paso 4: Trabajando con App Reviews y Entrega continua
---

TODO


