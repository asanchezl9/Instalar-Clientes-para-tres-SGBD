# **Práctica 2.3 - Instalar clientes para tres SGBD**
##### _Amparo Sánchez Ledo - ASIR 2 - 14/10/2026_

> Antes de comenzar, debemos tener en cuenta que en esta práctica vamos a instalar y probar clientes de consola, gráficos y web para poder conectarnos a los tres SGBD que instalamos en la tarea anterior (Oracle Database 23ai Free, PostgreSQL y MariaDB).

![Foto clientes SGBD](https://www.welldata.co.uk/wp-content/uploads/2021/06/5-databases-2.png)

> Nota: Deben estar funcionando los tres servidores y conocer la IP, puerto, base o servicio y el usuario de trabajo. Comprobaremos antes de comenzar, que la conexión de los tres SGBD que instalamos anteriormente es correcta con _nc -zv IP PUERTO_:

![Comprobación conexiones](img/ConexionesIniciales.png)

---

### **_Datos generales para las conexiones_**

| SGBD | Servidor | Base/servicio | Puerto | Usuario |
| :--- | :--- | :--- | :--- | :--- |
| **Oracle 23ai Free** | localhost o IP de VM | FREEPDB1 | 1521 | orauser |
| **PostgreSQL** | localhost o IP de VM | dbpg | 5432 | pguser |
| **MariaDB** | localhost o IP de VM | dbm | 3306 | muser |

> Nota sobre conexiones externas: Si te conectas desde otro equipo, verifica que la red lo permita. El comando nc -zv IP PUERTO confirma si el puerto está abierto y accesible, aunque no garantiza que las credenciales de inicio de sesión funcionen.

---

## **_1. Clientes para Oracle Database (en Mint/Debian)_**

> **Versión:** ...

### **_1.1 Cliente gráfico: SQL Developer_**

Comenzamos con los clientes para Oracle, donde el primero será el cliente gráfico SQL Developer. Lo primero es instalar un JDK compatible con la versión de SQL Developer que vayamos a descargar, así que debemos comprobarla primero. Primero actualizaremos la lista de paquetes, seguidamente instalaremos Java y mostraremos la versión de este:

```
sudo apt update

sudo apt install default-jdk unzip

java --version
```

![Versión de java](img/VersionJava.png)

Descargamos el ZIP de SQL Developer (enlace [aquí](https://www.oracle.com/database/sqldeveloper/technologies/download)) en nuestra máquina virtual:

![Descarga SQL Developer](img/sqldeveloper.png)

Accedemos a la carpeta de descargas con `cd Descargas` y vamos a descomprimir en /opt usando el nombre real del archivo con `sudo unzip NOMBRE_ARCHIVO_SQLDEVELOPER.zip -d /opt/`

E iniciamos SQL Developer con `/opt/sqldeveloper/sqldeveloper.sh`.

<!--(En este punto, ha saltado la incidencia 1)-->

![Inicio Oracle SQL Developer](img/sqldeveloper_bd.png)

---

> Opcional: Creamos un lanzador de menú. Para ello comprobaremos primero que las rutas de Exec e Icon coinciden con lo descomprimido. Ejecutaremos el comando `sudo tee /usr/share/applications/sqldeveloper.desktop > /dev/null <<'EOT'` y escribiremos lo siguiente:
```
[Desktop Entry]
Version=1.0
Name=SQL Developer
Comment=Entorno de desarrollo SQL
Exec=/opt/sqldeveloper/sqldeveloper.sh
Icon=/opt/sqldeveloper/icon.png
Terminal=false
Type=Application
Categories=Development;Database;
Keywords=database;db;sql;query;administration;
EOT
```

---

Y dentro de Oracle SQL Developer crearemos una conexión donde:

- Usuario: orauser
- Puerto: 1521
- Nombre de servicio: FREEPDB1

En caso de que el cliente esté en la misma VM (que es este caso) usaremos **localhost**; si está en otro equipo, usaremos la **IP del servidor** y comprobaremos que la red permita el acceso.

![Conexión usuario](img/ConexionSQL.png)

> Nota: Para comprobar que está bien, abajo a la izquierda debe salir **Estado: Correcto**.

---

### **_1.2 Cliente gráfico: VS Code y extensión SQL Developer_**

Pasamos a otro cliente gráfico. En este caso será el **VSCode y la extensión SQL Developer**. Lo primero será descargar el paquete .deb actual de VSCode (enlace [aquí](https://code.visualstudio.com/)) y una vez que lo tengamos descargado accedemos a la carpeta de **Descargas** (en caso de que no estemos en ella) e instalamos el paquete con los siguientes comandos:

![Descarga VSCode](img/descarga_vscode.png)

```
cd ~/Descargas

sudo apt install ./NOMBRE_DEL_ARCHIVO_CODE.deb
```

Seguidamente, abriremos VS Code (desde la terminal es poniendo `code` y se abre) y en las extensiones buscamos e instalamos la de **Oracle SQL Developer for VS Code**, configurando una conexión a **FREEPDB1** como **orauser**.

![Conexión SQL en VS Code](img/sql_vscode.png)

---

### **_1.3 Cliente de terminal: SQL*Plus_**

Para Mint (como es este caso) usaremos los ZIP oficiales de Oracle Instant Client para Linux x86-64 Basic y SQL*Plus de la misma versión (enlace [aquí](https://www.oracle.com/database/technologies/instant-client/downloads.html)) y la vamos a descomprimir ambas en un mismo directorio. La ruta será ~/oracle_amparo

```
mkdir -p ~/oracle_amparo

unzip ~/Descargas/NOMBRE_BASIC.zip -d ~/oracle_amparo

unzip ~/Descargas/NOMBRE_SQL_PLUS.zip -d ~/oracle_amparo

ls ~/oracle_amparo
```

Entraremos al directorio **instaclient_...** creado por los ZIP y comprobaremos los ejecutables. En caso de que sea necesario, configuraremos las rutas de bibliotecas dependiendo de las instrucciones oficiales de la versión:

> Nota: sustituye instaclient_XX_X por el directorio real

```
export LD_LIBRARY_PATH="$HOME/oracle-client/instantclient_XX_X:$LD_LIBRARY_PATH"

export PATH="$HOME/oracle-client/instantclient_XX_X:$PATH"

sqlplus -v

sqlplus orauser@//localhost:1521/FREEPDB1
```

> En caso de que la biblioteca **libaio** falta, instalaremos el paquete corresponiente a la distribución. Pedirá la contraseña en la terminal.

### **_1.4 Cliente de terminal: SQLcl_**

Empezaremos instalando Java y comprobando la versión:

```
sudo apt install default-jdk unzip

java --version
```

![Versión Java](img/VersionJava.png)

Descargamos SQLcl (enlace [aquí](https://www.oracle.com/database/sqldeveloper/technologies/sqlcl/download/)) y se descomprime el zip:

```
cd ~/Descargas

unzip NOMBRE_DEL_ARCHIVO_SQLCL.zip -d ~/sqlcl

~/sqlcl/sqlcl/bin/sql -version
```

> En caso de que se quiera poder ejecutar sql desde cualquier terminal, añade `export PATH="$HOME/sqlcl/sqlcl/bin:$PATH"` a ~/.bashrc y abre una terminal nueva 

Ahora, probaremos la conexión con orauser y FREEPDB1 (sin contraseña)

![Conexión orauser y FREEPDB1]()

---

## **_2. Clientes para postgreSQL_**

> **Versión:** ...

### **_2.1 Cliente de terminal: psql_**

Pasamos a PostgreSQL, al cliente de terminal psql: actualizaremos los repositorios, instalaremos el cliente, veremos la versión y nos conectaremos a la base de datos con **pguser** de manera segura:

```
sudo apt update

sudo apt install postgresql-client

psql --version

psql -h localhost -U pguser -d dbpg -W
```

> Instala el paquete correspondiente si se necesita una versión en concreto del cliente y ésta existe. Hay que comprobar antes la disponibilidad con `apt search postgresql-client`

---

### **_2.2 Cliente gráfico: pgAdmin 4_**

Empezaremos comprobando si la versión de Debian o la base de Ubuntu está admitida (consultando este [enlace](https://www.pgadmin.org/download/pgadmin-4-apt/)):

```
sudo apt install curl gnupg lsb-release

curl -fsS https://www.pgadmin.org/static/packages_pgadmin_org.pub | sudo gpg --dearmor -o /etc/apt/keyrings/packages-pgadmin-org.gpg

sudo sh -c 'echo "deb [signed-by=/etc/apt/keyrings/packages-pgadmin-org.gpg] https://ftp.postgresql.org/pub/pgadmin/pgadmin4/apt/UBUNTU_CODENAME pgadmin4 main" > /etc/apt/sources.list.d/pgadmin4.list'

sudo apt update

sudo apt install pgadmin4-desktop
```

> Nota sobre pgAdmin en Mint: El comando oficial falla porque lsb_release -cs devuelve el nombre de Mint, el cual no es admitido por el repositorio. Para solucionarlo, hay que seguir el apartado específico de Mint en su web oficial (cambiando ese valor por el de su base Ubuntu, como jammy o noble). Tras instalarlo, se abre pgAdmin desde el menú y se conecta a la base dbpg como pguser.

---

## **_3. Clientes para MariaDB_**

> **Versión:** ...

### **_3.1 Cliente de terminal: mariadb (o mysql)_**

Comenzaremos actualizando repositorios, instalando el cliente, viendo la versión de mariadb y probando la conexión a la base de datos con **muser**:

```
sudo apt update

sudo apt install mariadb-client

mariadb --version

mariadb -h localhost -u muser -p dbm
```

> Nota: El parámetro -p hará que el programa solicite la contraseña en la terminal.

---

### **_3.2 Cliente web: phpMyAdmin_**

Al igual que antes con mariadb, actualizamos repositorios e instalamos el cliente:

```
sudo apt update

sudo apt install apache2 php php-mysql php-mbstring php-zip php-gd php-curl phpmyadmin
```

Seguidamente habilitamos la extensión necesaria de PHP y reiniciamos el servidor Apache:

```
sudo phpenmod mbstring

sudo systemctl restart apache2
```

> Nota durante la instalación: Selecciona apache2 si el asistente lo pregunta y configura phpMyAdmin. La contraseña solicitada aquí es de almacenamiento interno; para conectarte luego, usa tu usuario muser.

> Ruta alternativa: Si la ruta web no se habilita automáticamente, ejecuta sudo a2enconf phpmyadmin y sudo systemctl reload apache2.

Finalmente, abrimos el navegador en [http://localhost/phpmyadmin/](http://localhost/phpmyadmin/) e iniciamos sesión con muser para comprobar la base de datos.

---

## **_4. Clientes para varios SGBD_**

> **Versión:** ...

### **_4.1 DBeaver Community_**

Descargamos el paquete .deb actual desde su [web oficial](https://dbeaver.io/download/) y lo instalamos mediante la terminal:

```
cd ~/Descargas

sudo apt install ./NOMBRE_DEL_ARCHIVO_DBEAVER.deb
```

Una vez abierto DBeaver, configuramos las tres conexiones apuntando a localhost (Oracle FREEPDB1 con orauser, PostgreSQL dbpg con pguser y MariaDB dbm con muser). Es normal que el programa solicite la descarga automática de los controladores JDBC al intentar conectar.

---

### **_4.2 Adminer_**

Adminer es un cliente web muy ligero compuesto por un único archivo PHP. Instalamos el paquete PHP CLI para la terminal, descargamos el archivo y levantamos un servidor local de prueba:

```
sudo apt install php-cli

wget https://www.adminer.org/latest.php -O adminer.php

php -S 127.0.0.1:8080 adminer.php
```

Para probarlo, abrimos el navegador en la misma máquina virtual entrando a [http://localhost:8080/](http://localhost:8080/). Seleccionamos el motor (PostgreSQL o MySQL/MariaDB) y probamos la conexión con los usuarios de trabajo correspondientes. Para apagar el servidor web desde la terminal, pulsamos Ctrl+C.

---

## **_Incidencias_**

#### **_Incidencia 1_**

Durante el inicio de SQL Developer (usando el comando `/opt/sqldeveloper/sqldeveloper.sh`) me ha saltado el error **Module javafx.base not found**: lo que ocurre es que Oracle ha dejado **AddLinuxVMOption --module-path=../../modules/javafx/linux-x64/lib**, pero la carpeta linux-64 no existe en el ZIP. Java intenta arrancar con --add-modules=javafx.base,... pero apuntando a un directorio vacío.

![Incidencia JavaFX](img/error1.png)

> El error se ha solucionado ejecutando ciertos comandos, cuyas finalidades son:

> 1. sed -i 's|...|...|' (Reparación): Sustituye la ruta errónea que venía de fábrica en el archivo de configuración de Oracle por la ruta correcta donde tu sistema Linux Mint almacena realmente las librerías gráficas de JavaFX.
> 2. grep -n module-path (Comprobación): Busca y muestra en pantalla la línea recién modificada para que puedas verificar visualmente que el cambio del paso anterior se ha guardado con éxito.
> 3. sed -i '/module-path/d' (Limpieza): Elimina cualquier rastro de configuración gráfica errónea en los archivos temporales de tu usuario, limpiando los intentos fallidos anteriores para evitar que interfieran en el arranque limpio.

> Los comandos completos son los siguientes:

```
sudo sed -i 's|--module-path=../../modules/javafx/linux-x64/lib|--module-path=/usr/share/openjfx/lib|' /opt/sqldeveloper/sqldeveloper/bin/java11.conf

grep -n module-path /opt/sqldeveloper/sqldeveloper/bin/java11.conf

# La línea 9 debe decir ahora AddLinuxVMOption --module-path=/usr/share/openjfx/lib

sed -i '/module-path/d' ~/.sqldeveloper/26.2.0/product.conf
```

---

## **_Fuentes consultadas_**
- Guión de la práctica: "Instalar clientes para tres SGBD".

- [Oracle (SQL Developer y SQLcl)](https://www.oracle.com/database/sqldeveloper/)

- [Oracle (Instant Client)](https://www.oracle.com/database/technologies/instant-client/downloads.html)

- [Visual Studio Code](https://code.visualstudio.com/)

- [pgAdmin 4](https://www.pgadmin.org/download/pgadmin-4-apt/)

- [DBeaver Community](https://dbeaver.io/download/)

- [Adminer](https://www.adminer.org/)

- Resolución de incidencias: Documentación de OpenJFX para el error de java11.conf.