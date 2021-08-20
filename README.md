- [Django](#django)
  - [Historia y Conceptos](#historia-y-conceptos)
    - [CGI Scripts](#cgi-scripts)
    - [WSGI](#wsgi)
    - [ASGI](#asgi)
    - [Frameworks](#frameworks)
    - [Django](#django-1)
      - [Características](#características)
      - [Ventajas](#ventajas)
  - [Carpetas en Django](#carpetas-en-django)
  - [Modelo MTV](#modelo-mtv)
    - [URLS](#urls)
    - [VIEWS](#views)
    - [TEMPLATES](#templates)
      - [Variables](#variables)
      - [Tags](#tags)
      - [Filtros](#filtros)
  - [Debug](#debug)
  - [ORM](#orm)
    - [Parámetros de los Campos](#parámetros-de-los-campos)
    - [Manipulación de los Datos](#manipulación-de-los-datos)
      - [INSERT](#insert)
      - [DELETE](#delete)
      - [UPDATE](#update)
      - [GET](#get)
      - [FILTER](#filter)
      - [ALL](#all)
  - [Apps](#apps)
  - [Admin](#admin)
    - [Crear Super Usuario](#crear-super-usuario)
    - [Editar características del Admin](#editar-características-del-admin)
  - [Modelo Proxy](#modelo-proxy)
  - [API REST](#api-rest)
    - [Características](#características-1)


# Django

## Historia y Conceptos
Al principio las web eran puramente texto plano **(Text-Based)**, posteriormente se requería conectar a Bases de Datos e implementar cierta lógica. 

### CGI Scripts 
`Common Gateway Interface` que permite ejecutar scripts dentro del servidor, es decir, un cliente puede solicitar datos en un programa ejecutado en un servidor web.

### WSGI
- `Web Service Gateway Interface` es una especificación que describe cómo se comunica un **servidor web** con una **aplicación web**.
- ***WSGI*** es un estándar de Python que permite escribir programas los cuales puedan comunicarse a través del protocolo HTTP, donde se establece una serie de reglas para que la comunicación sea estándar.
> Se encuentra inspirado en el estándar CGI
- Son servidores sincrónicos, por lo que no se puede implementar funcionalidades de ***Real Time***
- Django tiene servidores web integrados que se suelen usar para pruebas, y cuando se lance a producción la aplicación generalmente se usa **Nginx** para hacer proxy inverso y **Gunicorn** como servidor WSGI.

### ASGI
`Asynchronous Server Gateway Interface` son Servidores Asíncronos, que permiten realizar implementaciones de ***Real Time***, manejando Web Sockets. La parte Web realiza el proceso síncrono y la parte de Web Sockets realiza el proceso asíncrono.

### Frameworks
Surgen para resolver varias tareas habituales en el desarrollo web como: Peticiones HTTP, conexión a BD, interacción con el HTML, entre otras.

### Django 
Nace en 2003. Busca generar las cosas mas sencillas para los sitios grandes, ofreciendo **URL's simples**, generando objetos **Request y Response** para las peticiones, además de la inclusión de **ORM** (Manejar la BD mediante objetos del lenguaje)

#### Características
- Rápido Desarrollo
- Listo para todo con muchas herramientas
- Seguridad
- Escalable
- Versátil

#### Ventajas
- Uso de Python
- Comunidad activa
- Open Source

## Carpetas en Django
- `__init__.py`: Declara el proyecto como un módulo de Python.
- `settings.py`: Archivo mas importante que define todas las configuraciones del proyecto.
	- `BASE_DIR`: Declara el lugar donde está corriendo el proyecto.
	- `SECRET_KEY`: Usado para el hashing de las contraseñas y las sesiones en la BD.
	- `DEBUG`: Marca que el proyecto está en desarrollo _(True)_, o en producción _(False)_.
	- `ALLOWED_HOSTS`: Almacena los Hosts permitidos para interactuar con el proyecto.
	- `INSTALLED_APPS`: Almacena las aplicaciones instaladas.
	- `MIDDLEWARE`: Permite ejecutar ciertas acciones antes o después de una petición.
	- `ROOT_URLCONF`: Archivo principal de urls.
	- `WSGI_APLICATION`: Archivo de entrada WSGI.
	- `DATABASE`: Configuración de la BD. Se puede utilizar cualquier gestor.
	- `AUTH_PASSWORD_VALIDATIONS`: Validaciones de contraseñas del proyecto. Se puede crear validadores personalizados.
	- `LANGUAGE_CODE`: Lenguaje de la aplicación.
	- `TIME_ZONE`: Zona horaria.
	- `STATIC_URL`: Define que cuando se accede a `/static` busca el archivo estático solicitado.
- `urls.py`: Archivo principal. Es el punto de entrada de todas las peticiones. 
- `wsgi.py`: Archivo usado durante el deployment para producción. 
- `asgi.py`: Es un punto de entrada para que los servidores web compatibles con ASGI puedan servir su proyecto, como Node.js u otros.
- `manage.py`: Archivo que no se modifica, pero es con el cual se interactúa durante el desarrollo.

## Modelo MTV
En Django las URLS se encargan de buscar el recurso, iterando por cada URL hasta encontrar la petición y relacionarlo con la vista especificada. La vista se encarga de traer los datos.

El **MTV** es similar al **MVC**, donde:
- `Modelo/Modelo`: Define la estructura de los datos
- `Template/View`: Define la lógica de presentación de datos
- `View/Controller`: Encargado de traer los datos y pasarlos al Template. Es el que comunica el Modelo con el Template.

### URLS

En el archivo `urls.py` agregamos todas las url's del proyecto ingresándolas en la lista `urlpatterns`. Podemos agregar url's con y sin variables. A continuación un ejemplo de cada una.

Para agregar una url se utiliza la función `path()`, la cual recibe los siguientes parámetros.
- `route`: La dirección de url.
- `view`: La vista que se llama cuando usamos dicha url.
- `name`: El nombre por defecto de la url. Es muy útil utilizarla en los diferentes archivos del proyecto porque este nunca cambiará. Así podremos editar la variable `route` sin problema alguno. 

```py
path('hello_world/', local_views.hello_world, name='hello_world')
```

Para agregar una url con variables lo hacemos utilizando `<>`, y dentro de ellas especificamos `<tipo_dato:nombre>`, de la siguiente manera.

```py
path('hi/<str:name>/<int:age>/', local_views.say_hi, name='hi')
```
Esta url recibe 2 variables, la primera es un string llamado nombre, y la segunda es un entero llamado edad.

### VIEWS

En Django tenemos el archivo `views.py`, el cual es el equivalente al `Controlador` en el modelo ***MVC***. Aquí se recibe un objeto `HttpRequest` donde va la información de la solicitud por GET o POST, se realiza la lógica deseada mediante código y luego se devuelve una respuesta usando un objeto `HttpResponse`, que normalmente es en HTML.

```py
def hello_world(request):
	now = datetime.now().strftime('%b  %dth, %Y - %H:%M hrs')
	return HttpResponse(f'Oh, hi! Current server time is {now}')
```
En la anterior vista se almacena la fecha actual usando `datetime` de Python, luego se devuelve usando un objeto HttpResponse directamente.

Otra alternativa es usar la función `render()`, la cual recibe los siguientes parámetros principales:

- `request`: Siempre se debe agregar como primer parámetro.
- `template`: Se agrega el nombre del template en donde se mostrará la información.
- `context`: Es un diccionario el cual contiene las variables que deseemos mostrar en el template.

```py
def list_posts(request):
	posts = Post.objects.all().order_by('-created')
	return render(request, 'posts/feed.html', {'posts': posts})
```
La anterior vista almacena todos los registros de la tabla `Post` en la BD. Posteriormente retorna mediante la función `render()` el request (obligatorio), el template y el contexto. Este template es el que se muestra finalmente al usuario, ya que es casi siempre un archivo HTML.

> Recordar que las funciones de cada vista deben recibir obligatoriamente como parámetro el request.

### TEMPLATES

Los Templates en Django son el equivalente a las `Vistas` en el modelo ***MVC***. Estos se almacenan en una carpeta de Django llamada `/templates`.  Aquí es donde se muestra la información finalmente al usuario, es decir, la interfaz gráfica. Los Templates tienen distintas funcionalidades que permiten desarrollar de una mejor forma, donde podemos incluir contenido estático, y contenido dinámico, mediante el uso de una sintaxis especial la cual se explicará a continuación.

#### Variables
Las variables son pasadas en el `contexto` (Diccionario), el cual se recibe desde la `Vista`. En el `Template` podemos hacer uso de estas variables, por ejemplo:
```py
# views.py
render(request, 'index.html', {name: 'Bryann'})
```
```django
{# index.html #}
Mi nombre es {{ name }}
```
En el HTML se mostrará el texto: 
```html
Mi nombre es Bryann
```
#### Tags
Proveen una lógica en el proceso de representar los datos. Uno de los tags mas usados son los que permiten realizar condiciones o ciclos, haciendo uso de estructuras de control conocidas.
```django
{# For #}
{% for name in names %}
	{{name}}
{% endfor %}

{# If #}
{% if  name %}
	{{name}}
{% endif %}
```
También podemos utilizar un tag que nos protege de ataques por CSRF, el cual es un tipo de exploit malicioso en el que se ejecutan acciones no autorizadas por un usuario aprovechándose de ciertos privilegios. Este tag se agrega normalmente en los formularios.
```django
{% csrf_token %}
```

#### Filtros
Estos transforman los valores de las variables y argumentos, por ejemplo:
```py
# views.py
render(request, 'index.html', {notice: 'django es el mejor framework de python'})
```
```django
{# index.html #}
Titular: {{ notice|title }}
```
En el HTML se mostrará el texto ***(Capitaliza cada palabra)*** 
```html
Titular: Django Es El Mejor Framework De Python
```

> Los comentarios en Django van dentro de estas llaves {#  #}, y funcionan como cualquier otro en un lenguaje de programación.

## Debug

Una manera muy útil de hacer debug es usando un módulo estándar de Python, el cual me permite ver los valores de las diferentes variables en ejecución por medio de la consola.

Importamos el módulo

```py
import pdb
```
Lo utilizamos en la parte de código que necesitemos hacer el debug llamando al método `set_trace()`, por ejemplo:

```py
def sort(request):
	numbers = request.GET['numbers'].split(',')
	pdb.set_trace() # Debug por consola
	sorted_numbers =  sorted([int(x) for x in numbers])
	
	data = {
		'status': 'ok',
		'numbers': sorted_numbers,
		'message': 'Integers sorted successfully.'
	}
	
return JsonResponse(data)
```
En este caso podremos ver el valor de `request.GET` y de `numbers` por consola, y asegurarnos que todo esta correctamente, o revisar por qué motivo no funciona nuestro código.
```django
(Pdb) request.GET
<QueryDict: {'numbers': ['3,44,14,13,52,5']}>
(Pdb) numbers
['3', '44', '14', '13', '52', '5']
```

## ORM

Es el encargado de permitir el acceso y control de una base de datos relacional a través de una abstracción a clases y objetos. De manera más sencilla, es un conjunto de clases que permiten interactuar con la BD.
En `models.py` se agrega las tablas de la BD, entendiendo que una clase es una tabla y siempre debe heredar de `models.Model`

### Parámetros de los Campos

- `auto_now_add`: Almacena la fecha en que se creo el registro. ***Booleano***.
- `auto_now`: Almacena la fecha en que se actualizó el registro. ***Booleano***.
- `required`: Obliga a que el campo sea ingresado. ***Booleano***
- `blank`: El campo puede estar en blanco. ***Booleano***
- `max_length`: Determina la longitud máxima del campo. ***Entero***

Una vez creados los modelos debemos ejecutar los siguientes comandos:
```batch
py manage.py makemigrations
py manage.py migrate
```
- `makemigrations:` Crea la migración ***(Genera las sentencias SQL)***
- `migrate:` Ejecuta la migración ***(Ejecuta los comandos SQL)***

> 1. `migrate` depende de `makemigrations` para hacer cambios en la BD, por lo que debe realizarse en ese orden.
> 2. Django agrega por default el campo `id` en las tablas de la BD.

### Manipulación de los Datos

Para manipular los datos de la BD podemos ejecutar el Shell de Django, mediante el siguiente comando.

```batch
py manage.py shell
```
Posteriormente debemos importar la tabla que deseemos manipular, como por ejemplo:

```batch
from posts.models import User
```
Ahora si podremos hacer todas las operaciones correspondientes de la siguiente forma.

#### INSERT
Inserción de datos ***(Nuevo registro)***
```batch
user = User.objects.create(
	email='user@gmail.com',
	first_name='user',
	last_name='smith',
)
user.save()
```

#### DELETE
Borrar datos ***(Eliminar registro)***
```batch
user = User.objects.get(pk=1)
user.delete()
```

#### UPDATE
Actualizar datos ***(Actualizar registro)***
```batch
user = User.objects.get(pk=1)
user.password = '2435353'
user.save()
```

#### GET
Obtener datos ***(Obtener registro)***

```batch
user = User.objects.get(pk=1)
user = User.objects.get(email='bryann@gmail.com')
```

#### FILTER
Filtrar datos (Consultar registros mediante filtros específicos). En este caso `users` será una lista con todos los objetos que cumplan la condición.

```batch
users = User.objects.filter(email__endswith='@gmail.com')
```

#### ALL
Obtener todos los datos ***(Obtener todos los registros)***. `users` es un QuerySet con todos los objetos de la tabla en la BD. Se puede acceder a ellos como si fuera una lista de listas.
```batch
user = User.objects.all()
```

## Apps
Conjunto de código que se encarga de resolver una parte muy específica del proyecto, contiene sus modelos, vistas, url's, etc.
Para crear aplicaciones en Django ejecutamos el siguiente comando. _(Se recomienda que el nombre sea en plural,  minúsculas y en inglés)_

```bash
python manage.py startapp name_app
```

 Se crea una carpeta de la aplicación que contiene los siguientes archivos:
 - `/migrations`: Se encarga de grabar los cambios en la BD.
 - `admin.py`: Se encarga de registrar los modelos en el administrador de Django.
 -  `apps.py`: Declara toda la configuración de la aplicación.
 - `models.py`: Define los modelos de la BD.
 - `tests.py`: Permite hacer pruebas.

## Admin
Django nos provee un administrador con una interfaz gráfica para facilitar la manipulación de los datos.

### Crear Super Usuario
Ejecutamos el siguiente comando 
```batch
 py manage.py createsuperuser
```
Posteriormente llenamos toda la información solicitada por Django.
```batch
 Username: bryann
 Email address: bryan@mail.com
 Password: my_pass
 Password (again): my_pass
 
 Superuser created successfully
```

### Editar características del Admin

En la clase `admin.py` podemos registrar los diferentes modelos de nuestro proyecto, además de poder editar las características de cómo se muestra, y cómo se interactúa con la información en la interfaz. Estas variables se utilizarán mas adelante en el Modelo Proxy.

- `list_display` : Se registran los campos para mostrar en la tabla
- `list_display_links` : Se agrega un link a cada campo para abrir el detalle.
- `list_editable` : Se agregan los campos que deseemos editar directamente en la tabla sin ir al detalle.
- `search_fields` : Se registran los campos que deseemos filtrar en la búsqueda. (Para acceder a los campos del `user` que provee Django debemos llamar las variables de esta manera,  `user__nombreCampo`).
- `list_filter` : Podemos agregar los campos por los que podemos filtrar en el admin. Es otra forma de filtrar además de la búsqueda.
> Solo se puede agregar cualquier campo a `list_display_links` o `list_editable`, pero no a los dos.


## Modelo Proxy

Podemos implementar usuarios ya definidos por Django con propiedades por default, como *first_name, last_name, password, etc.*, además de crear los campos adicionales que necesitamos según el proyecto. 

> También se puede realizar heredando de la Clase `AbstractUser`.

Primero importamos la librería de User, y la de modelos que ya viene implementada cuando se crea una aplicación.
```py
from django.db import models
from django.contrib.auth.models import User
```
Luego creamos la Clase que será nuestro modelo (Tabla)
```py
class Profile(models.Model):
```
Dentro de la clase agregamos el campo del Usuario, el cual es de tipo `OneToOneField`, el cual permite vincular el modelo de usuario a este modelo creado. El parámetro `on_delete` nos dice la manera en que serán tratados los datos cuando se eliminan, y el valor `CASCADE` nos dice que cuando se elimina un objeto referenciado, también se eliminan los objetos que tienen referencias a él *(Llave Foránea)*.
```py
	user = models.OneToOneField(User, on_delete=models.CASCADE)
```
Agregamos los campos adicionales de nuestro modelo
```py
	website = models.URLField(blank=True)
	biography = models.TextField(blank=True)
	phone_number = models.CharField(max_length=20, blank=True)
	
	picture = models.ImageField(
		upload_to='users/pictures',
		blank=True,
		null=True)

	created = models.DateField(auto_now_add=True)
	modified = models.DateField(auto_now=True)

	# Método Mágico de Impresión
	def  __str__(self):
		return  self.user.username
```

En el anterior modelo se creó una clase (Tabla) llamada `Profile`, donde se implementa el usuario que provee Django, además de agregar campos personalizados adicionales, como es `website, biography, phone_number, picture, created y modified`. Por último hacemos los comandos correspondientes siempre que creamos o modificamos un modelo.

```batch
python manage.py makemigrations
python manage.py migrate
```

Ahora, debemos registrar el modelo `Profile` creado en el archivo `admin.py`. La manera más sencilla es la siguiente:
```py
from django.contrib import admin
from users.models import Profile

admin.site.register(Profile)
```

Sin embargo, podemos personalizar el admin con las características nombradas en la anterior sección. Primero creamos una clase que por convención debe llamarse `<nombre_modeloAdmin>` la cual hereda de `admin.ModelAdmin`. Además,  agregamos el decorador `@admin.register(<modelo>)` para hacer el registro.

```py
from django.contrib import admin
from users.models import Profile

@admin.register(Profile)
class  ProfileAdmin(admin.ModelAdmin):
	list_display = ('pk', 'user', 'phone_number', 'website', 'picture')
	list_display_links = ('pk', 'user')
	list_editable = ('phone_number', 'website', 'picture')
	search_fields = ('user__email', 'user__first_name',
	'user__last_name', 'phone_number')
	list_filter = ('created', 'modified', 'user__is_active', 'user__is_staff')
```
Aunque la Clase anterior puede estar vacía y funciona correctamente, el motivo por el que se crea es para editar las características de la interfaz de administrador, ya depende de lo que deseemos modificar.


##

## API REST

Es una aplicación web de lado del Back-End. Se configuran diferentes rutas que tienen cierta funcionalidad de interacción con la BD y lógica que puede ser consumido por cualquier tipo de cliente **(Protocolo HTTP)**

### Características

- Tiene métodos para hacer distintas cosas como: *Crear un Usuario, Hacer un Login,  Hacer un Registro, etc.*
- Cada ruta tiene usará métodos HTTP distintos, como ***GET, POST, PUT, DELETE, PATCH***
- Las rutas quedan disponibles para poder traer la información, que normalmente es devuelta en **XML** o **JSON**. 
> JSON es más utilizado por tener la información más ordenada y pesar menos.

