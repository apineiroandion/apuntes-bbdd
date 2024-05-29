
<img src="https://cdn.icon-icons.com/icons2/2699/PNG/512/postgresql_logo_icon_170836.png" width="440" height="230">


# Gestión de usuarios y bases de datos 🖥️
Por defecto se conecta al esquema **public**. Si quiero acceder a otro esquema necesito tener permiso para entrar, una vez dentro también se necesita permisos para acceder a cada tabla.
-  **\l** - Ver lista de bases de datos.
- **\du** - Ver lista de usuarios (y sus roles asignados).
- **\dt** - Ver lista de tablas. (**\dt schemaName.*** - Para ver las tablas de un esquema concreto)
	- **\dt e.**- Muestra las tablas de los esquemas que empiezan por la letra ***e*** (lleva asterisco a cada lado del punto)
- **\dn** - Ver lista de esquemas.
	- **\dn+** - Ver lista de esquemas con sus permisos (UC = **U**sage - **C**reate table).
- **\dp** - Ver lista de privilegios en tablas (tabla de privilegios más abajo).
- **\d nombre_tabla** - Muestra la descripción de una tabla.
- **\z es3.* ** - Muestra los privilegios de acceso para todas las tablas en el esquema ***es3***.
- **\l** - Muestra una lista de las bases de datos disponibles junto con sus permisos.
- **select user** - Muestra el usuario actual en el que me encuentro.
- **\c nameDatabase nameUser** - Para entrar a una base con un usuario determinado.
	- **\c nameDatabase** - Para conectarse a una base de datos.
## Creación de la estructura y uso (ACL)
### Creación de una base de datos:
1. **create database dam;** - Crea una base de datos con nombre _dam_.
2. Para borrar una base de datos, primero se pone **exit**:
	- **dropdb -U postgres futbol2** - Elimina la base de datos *futbol2* (desde la terminal principal).
3. **psql dam postgres** - Para conectarnos a una base de datos y un usuario a la vez. Se introduce la base de datos (**_dam_**) junto al usuario que vamos a utilizar (**_postgres_**).
4. **\c postgres** - Nos conecta a otra base de datos (_postgres_ en este caso).
### Creación de un nuevo usuario:
1. **create user a with password 'a';** - Crea un usuario con nombre **_a_** y con contraseña **_a_**.
	- **drop user a** - Eliminar usuario (**_a_** en este caso).
2. **select user;** - Muestra el usuario al que estoy conectado actualmente.
3. **\c dam a** - Conectarse a la base de datos (**_dam_**) desde el usuario **_a_**. Utilizar la contraseña del usuario si la pide.
4. **exit** - Para salir del usuario
### Creación de un nuevo esquema:
1. **create schema a authorization a;** - Crea un esquema llamado **_a_** cuyo propietario será **_a_**.
	- **drop schema a authorization a;** - Elimina el esquema llamado **_a_** con propietario **_a_**.
	- **drop schema es3;** - Elimina el esquema ***es3***.
### Creación de una nueva tabla:
1. **create table t1(a int);** - Crea una tabla con nombre **_t1_** que contiene un entero con nombre **_a_**.
	- **create table c.t1 (a int);** - Crea la tabla en el esquema indicado (**_c_** en este caso)
2. **insert into t1 values(1);** - Insertar valores a una tabla.
3. **drop table fu.xoga** - Para eliminar las estructura y contenido de una tabla, en este caso la tabla **xoga** del esquema **fu**.
4. **delete from adestra** - Borra el contenido de la tabla **adestra**.
### Creación de vistas:
1. **create view v as select codx,nomx,codequ from xogador;** - Crea una vista con los parámetros seleccionados.
2. **create view v2 as select codx,nomx,xogador.codequ,nomequ from xogador,equipo where xogador.codequ=equipo.codequ and xogador.codequ='e1';** - También se puede crear con condiciones
3. **drop view v;** - Para eliminar la vista creada (***v***).
## Comandos de permisos
#### Permisos de esquemas
- (Permiso esquema) **grant USAGE on schema nameSchema to nameUser;** - Da permisos a un usuario para usar el esquema.
#### Permisos de propietarios
1. **alter schema es4 owner to u4;** - Hace propietario del esquema **_es4_** al usuario **_u4_**.
#### Permisos de lectura
1. (Permiso tablas) **grant select on nameTable to nameUser;** - Dar permisos para poder hacer **select** de una tabla a otro usuario. No se pueden ver las tablas sin antes haber obtenido permisos para entrar al esquema (punto anterior).
2. **alter default privileges in schema a grant select on tables to b;** - Asegura que cualquier nueva tabla creada en el esquema `a` automáticamente otorgará el privilegio `SELECT` al usuario `b`.
#### Permisos de creación
1. **grant create on database dam to u3;** - Da permisos para crear esquemas en la base de datos ***dam*** al usuario ***u3***.
#### Permisos de update
1. **grant update(codest) on partido to u3 with grant option;** - Da permisos al usuario ***u3*** para hacer update de ***codest*** de la tabla ***partido***.
#### Permisos de insertar (append)
1. **grant insert on t4 to b;** - Concede permisos al usuario (**_b_**) para insertar elementos en la tabla ***t4***.
	- **grant insert on equipo to u4 with grant option;** - _grant option_ hace que desde el usuario al que se le da permiso, puedas dar permisos a otros nuevos usuarios.
2. **revoke insert on t4 from b;** - Quita privilegios para insertar datos en la tabla ***t4*** al usuario ***b***
#### Permisos de conexión
1. **grant connect on database dam to u5** -  Concede permiso al usuario ***u5*** para conectarse a la base de datos ***dam***.
2. **revoke connect on database dam from u5;** - Quita privilegios para conectarse a la base de datos ***dam*** al usuario ***u5***. 
## Roles de usuario
1. **create role readonly** - Para crear un rol.
2. **grant readonly to u4;** - Da el rol ***readonly*** al usuario ***u4***. Recibirá todos los permisos que tenga el rol asignado.
	- **grant ventas to alicia;** - Mete a ***alicia*** en el rol ***ventas***.
3. **grant connect on database dam to readonly;** - Concede permisos al rol (***readonly***) para conectarse a la base de datos ***dam***.
4. **grant insert on es3.xogador to readonly;** - Concede permisos de inserción en la tabla ***xogador*** del esquema ***u3*** al rol ***readonly**.
## Copias de seguridad
### Crear (poner siempre exit antes)
1. Poner ***exit***.
2. ***pg_dump -U postgres -Fp futbol2 > f2.dump*** - Aplicación externa que crea una copia de ***futbol2*** en un fichero externo llamado ***futbol2.dump***. 
	- (**-Fc** comprime el archivo, mientras que **-Fp** lo deja en texto plano)
3. La copia se crea por defecto en el directorio en el que nos encontramos.
4. Si lo abrimos veremos información relativa a la copia, junto con la copia del propio fichero.

También se pueden tomar filtros para hacer exportaciones:
1. **pg_dump -U postgres --table='fu.x*' -Fp futbol2'** - Exporta solo las tablas del esquema **fu** que empiecen por **x**.
2. **pg_dump -U postgres --table='fu.x*' --table='fu.interven' --table='fu.adestra' -Fp futbol2 > fup2.dump** - Exporta solo las tablas del esquema **fu** que empiecen por **x**, y que contengan **interven** e **adestra**, y se copia en el fichero **fup2.dump**.
3. **pg_dump -U postgres -a --table='fu.x*' --table='fu.interven' --table='fu.adestra' -Fp futbol2 > fup2.dump** - Si se le añade **-a** solo va a exportar los datos de las tablas, no las tablas enteras (no podrá volver a crear las tablas).
Algunos de los parámetros más útiles para la creación de copias de seguridad:

|                 Tipo                 |                        Uso                         |
| :----------------------------------: | :------------------------------------------------: |
|                **-a**                |        Exporta solo los datos de la tabla.         |
|        **--disable-triggers**        |             Deshabilita los triggers.              |
|            **--inserts**             |          Se ven los inserts en la copia.           |
|             **--column**             | Se ven las columnas en las que se meten los datos. |
|         **-n 'nameSchema'**          |        Exporta solo el esquema que pongas.         |
|         **-N 'nameSchema'**          |  Exporta todos los esquemas menos el que pongas.   |
| **--exclude-table-data='nameTable'** |    Excluye los datos de la tabla seleccionada.     |
|          **-t 'nameTable'**          |           Selecciona la tabla nombrada.            |
|                **-c**                |  Hace un borrado primero y luego crea las tablas.  |
### Restaurar (poner siempre exit antes)
1. Para restaurar y utilizar una copia de seguridad hecha con **-Fp** se crea una base de datos, nos metemos dentro de la base de datos y se lanza el script utilizando **\i (directorio)**
2. Para restaurar y utilizar una copia de seguridad hecha con **-Fc** se utiliza: **pg_restore -U postgres -C -d postgres f2c.dump** - Crea la base de datos directamente con el nombre que tenia la base anterior.
3. Para restaurar una base de datos pero *cambiando el nombre de la base de datos*:
	1. Poner **exit**
	2. **createdb -U postgres -T template0 futbolrestaurada**
	3. **pg_restore -U postgres -d futbolrestaurada f2c.dump** - Restaura la base de datos **f2c.dump** pero en otra base de datos llamada **futbolrestaurada**.
4. **pg_restore -l fup2c.dump > l.txt** - Genera un fichero llamado **l.txt** y manda una copia resumida de **fup2c.dump**.
## Inserts
1. **insert into facturas values (202346, true, 'descuento en volumen', '{"detalles":"..."}');** - Permite insertar datos a la tabla ***factura*** (en este caso);
## Row level security (conectarse como postgres) (RLS)
1. **alter table facturas enable row level security** - Conectar nivel de seguridad bajo a nivel de fila. Por defecto no tiene políticas por lo que ningún usuario tiene acceso a filas. Hay que dar alguna política permisiva.
2. **alter table facturas disable row level security;** - Quita todas las restricciones a nivel de fila.
3. **\d facturas** - Para ver las descripción de la tabla ***facturas***, de esta forma se ven las políticas que tiene la tabla.
### Crear y eliminar políticas
1. **create policy solo_finalizada on facturas to contabilidad using (finalizada=TRUE);** - Crea una política permisiva (que solo puede usar las que tengan el campo finalizada=true) con nombre ***solo_finalizada***, en la tabla ***facturas*** para el rol ***contabilidad***.
	- **drop policy solo_finalizar on facturas** - Eliminar la política.  
2. **create policy todas_las_filas on facturas to ventas using (TRUE);** - Crea la política (para ver todo) con nombre ***todas_las_filas***, en la tabla ***facturas*** para el rol ***ventas***.
3. **create policy non_borrar on facturas as restrictive for delete to public using (finalizada=false)**; - Crea una política restrictiva. Hace que se puedan borrar las que cumplan finalizada=false.
4. drop policy todas_filas on facturas; - Para eliminar la política ***todas_filas*** de la tabla ***facturas***.

Postgres aplica **primeiro a suma (OR)** das politicas permisivas, e logo, sobre as filas que quedan aplica a** multiplicacion (AND)** das politicas restrictivas.
As politicas restrictivas seleccionan para borrar aquelas filas que cumplan ca expresion ***Using*** utilizada.
## Transacciones
1. **BEGIN** - Dentro de una transacción, puedes ejecutar múltiples operaciones (INSERT, UPDATE, DELETE, etc.) que se considerarán parte de una única unidad de trabajo. Estas operaciones no se harán efectivas hasta que se ejecute el comando `COMMIT`. Hasta entonces, los cambios son visibles solo dentro de la transacción.
2. **ROLLBACK** - Revierte todos los cambios realizados en la base de datos durante la transacción actual. Después de ejecutar `ROLLBACK`, la base de datos vuelve a su estado anterior al `BEGIN`.
3. **COMMIT** - Hace permanentes todos los cambios realizados durante la transacción actual. Después de ejecutar `COMMIT`, los cambios son visibles para otros usuarios y sesiones de la base de datos.

**Ejemplo práctico de uso:**
```sql
empresa=> BEGIN;
BEGIN
empresa=*> DELETE FROM facturas;
DELETE 1
empresa=*> ROLLBACK;
ROLLBACK
```
1. **Iniciar una Transacción**:
	```sql
	empresa=> BEGIN;
	BEGIN
	```
	- Esto inicia una nueva transacción. Las operaciones siguientes se ejecutarán dentro de esta transacción.
2.  **Eliminar una Fila**:
	```sql
	empresa=*> DELETE FROM facturas;
	DELETE 1
	```
	- Este comando elimina una fila de la tabla `facturas`. Sin embargo, este cambio no es visible fuera de la transacción hasta que se ejecute `COMMIT`.
3. **Revertir la Transacción**:
	```sql
	empresa=*> ROLLBACK;
	ROLLBACK
	```
	- Este comando revierte todos los cambios realizados durante la transacción actual, es decir, la eliminación de la fila de la tabla `facturas` no se hará efectiva en la base de datos.

**En resumen:**
- **`BEGIN`**: Inicia una transacción.
- **`ROLLBACK`**: Revierte la transacción y todos sus cambios.
- **`COMMIT`**: Finaliza la transacción y hace permanentes todos sus cambios.

Estas operaciones son fundamentales para garantizar la integridad y consistencia de los datos en una base de datos, permitiendo realizar múltiples operaciones como una sola unidad de trabajo y proporcionando la capacidad de deshacer los cambios si es necesario.
## Comandos útiles:
- **\dt information_schema.* **- Muestra la informacion de los esquemas.
- **sudo pluma /etc/postgresql/13/main/pg_hba.conf** - Entrar en el archivo de configuración.
- **sudo systemctl reload postgresql** - Reinicia el servidor de postgres.
- **show search_path** - Busca la ruta en la que estamos.
- **alter user u3 in database dam set search_path to es3;** - Cambia el path para que busque los esquemas por **_es3_** u no por el nombre de usuario **_u3_**, para que funcione correctamente el comando \dt. (Salir y entra de nuevo al usuario para que se aplique).
- **select * from pg_tables where tableowner='u3';** - Muestra información de las tablas de usuarios. 
