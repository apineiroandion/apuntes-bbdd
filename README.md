
<img src="https://cdn.icon-icons.com/icons2/2699/PNG/512/postgresql_logo_icon_170836.png" width="440" height="230">


# Gestión de usuarios y bases de datos 🖥️
Por defecto se conecta al esquema **public**. Si quiero acceder a otro esquema necesito tener permiso para entrar, una vez dentro también se necesita permisos para acceder a cada tabla.
-  **\l** - Ver lista de bases de datos.
- **\du** - Ver lista de usuarios (y sus roles asignados).
- **\dt** - Ver lista de tablas. (**\dt a.*** (**a** es el nombre del usuario que queremos ver))
- **\dn** - Ver lista de esquemas.
-  **\dp** - Ver lista de privilegios.
## Pasos para la creación de una base de datos:
1. **create database dam;** - Crea una base de datos con nombre _dam_.
2. Para borrar una base de datos, primero se pone **exit**:
	- **dropdb -U postgres futbol2** - Elimina la base de datos *futbol2*
3. **psql dam postgres** - Para conectarnos a una base de datos y un usuario a la vez. Se introduce la base de datos (**_dam_**) junto al usuario que vamos a utilizar (**_postgres_**).
4. **\c postgres** - Nos conecta a otra base de datos (_postgres_ en este caso).

## Pasos para crear una nuevo usuario:
1. **create user a with password 'a';** - Crea un usuario con nombre **_a_** y con contraseña **_a_**.
	- **drop user a** - Eliminar usuario (**_a_** en este caso).
2. **select user;** - Muestra el usuario al que estoy conectado actualmente.
3. **\c dam a** - Conectarse a la base de datos (**_dam_**) desde el usuario **_a_**. Utilizar la contraseña del usuario si la pide.
4. **exit** - Para salir del usuario

## Pasos para crear una nuevo esquema:
1. **create schema a authorization a;** - Crea un esquema llamado **_a_** cuyo propietario será **_a_**.
	-  **drop schema a authorization a;** - Elimina el esquema llamado **_a_** con propietario **_a_**.
## Pasos para crear una nueva tabla:
1. **create table t1(a int);** - Crea una tabla con nombre **_t1_** que contiene un entero con nombre **_a_**.
	- **create table c.t1 (a int);** - Crea la tabla en el esquema indicado (**_c_** en este caso)
2. **insert into t1 values(1);** - Insertar valores a una tabla.
3. **drop table fu.xoga** - Para eliminar las estructura y contenido de una tabla, en este caso la tabla **xoga** del esquema **fu**.
4. **delete from adestra** - Borra el contenido de la tabla **adestra**.
## Comandos de permisos
- (Permiso esquema) **grant USAGE on schema a to b;** - Desde el usuario **_a_**, da permisos al usuario **_b_** para entrar al esquema.
- **grant select,insert on table es4.estadio to u5 with grant option;**- Dar ambos permisos a la vez.
#### Permisos de propietarios
1. **alter schema es4 owner to u4;** - Hace propietario del esquema **_es4_** al usuario **_u4_**.
#### Permisos de lectura
1. (Permiso tabla) **grant select on t1 to b;** - Desde el usuario donde está la tabla t1 (**_a_**), permite darle permiso a otro usuario para ver la tabla (**_b_**). No se pueden ver las tablas sin antes haber obtenido permisos para entrar al esquema (punto anterior).
2. **alter default privileges in schema a grant select on tables to b;** - Da permiso para que un usuario (**_a_**), pueda ceder permisos para **ver todas las tablas** a otro usuario (**_b_**), tanto para ver las tablas actuales como las futuras
#### Permisos de insertar
1. **grant insert on t4 to b;** - Concede permisos a la tabla (**_b_**) para insertar elementos.
	- **grant insert on equipo to u4 with grant option;** - _grant option_ hace que desde el usuario al que se le da permiso, puedas dar permisos a otos nuevos usuarios.
2. **revoke insert on t4 from b;** - Permite retirar permisos de insertar a la tabla (**_b_**)
#### Permisos de conexión
1. **grant connect on database dam to u5** -  Concede permiso a la base de datos ***dam*** para conectarse a la base de datos ***u5***.
2. **revoke connect on database dam from u5;** - Permite retirar permisos de conexión a la base de datos ***database***
## Privilegios
- Tipos de privilegios: **a=arwdDxt/a**
	- **_/a_** - Quien concede los permisos. (**_a_** es el nombre del usuario)
	- Un ***** significa que puede darle permisos a otros usuarios.

|    Tipo    | Abreviatura |
| :--------: | :---------: |
|   INSERT   |   ***a***   |
|   SELECT   |   ***r***   |
|   UPDATE   |   ***w***   |
|   DELETE   |   ***d***   |
|  TRUNCATE  |   ***D***   |
| REFERENCES |   ***x***   |
|  TRIGGER   |   ***t***   |
|   USAGE    |   ***U***   |
|   CREATE   |   ***C***   |
|  CONNECT   |   ***c***   |
|  DATABASE  |  ***CTc***  |
## Roles de usuario
1. ***create role readonly*** - Para crear un rol.
2. ***grant readonly to u4*** - Dar el rol a un usuario (***u4*** en este caso). Recibirá todos los permisos que tenga el rol asignado.
3. **grant connect on database dam to readonly;** - Concede permisos al rol (***readonly**) para conectarse a una base de datos.
4. **grant insert on es3.xogador to readonly;** - Concede permisos de inserción en la tabla **xogador** de **u3** al rol (***readonly**).
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
### Restaurar (poner siempre exit antes)
1. Para restaurar y utilizar una copia de seguridad hecha con **-Fp** se crea una base de datos, nos metemos dentro de la base de datos y se lanza el script utilizando **\i (directorio)**
2. Para restaurar y utilizar una copia de seguridad hecha con **-Fc** se utiliza: **pg_restore -U postgres -C -d postgres f2c.dump**
3. Para restaurar una base de datos pero *cambiando el nombre de la base de datos*:
	1. Poner **exit**
	2. **createdb -U postgres -T template0 futbolrestaurada**
	3. **pg_restore -U postgres -d futbolrestaurada f2c.dump** - Restaura la base de datos **f2c.dump** pero en otra base de datos llamada **futbolrestaurada**.
4. **pg_restore -l fup2c.dump > l.txt** - Genera un fichero llamado **l.txt** y manda una copia resumida de **fup2c.dump**.
### Inserts
1. **insert into facturas values (202346, true, 'descuento en volumen', '{"detalles":"..."}');** - Permite insertar datos a la tabla ***factura*** (en este caso);
### Row level security (conectarse como postgres)
1. **alter table facturas enable row level security** - Conectar nivel de seguridad bajo a nivel de fila.
2. **\d facturas** - Para ver las descripción de la tabla, de esta forma se ven las políticas que tiene la tabla.
3. **create policy solo_finalizada on facturas to contabilidad using (finalizada=TRUE);** - Crea la política (que solo puede usar las que tengan el campo finalizada=true) con nombre ***solo_finalizada***, en la tabla ***facturas*** para el rol ***contabilidad***.
	- **drop policy solo_finalizar on facturas** - Eliminar la política.  
4. **create policy todas_las_filas on facturas to ventas using (TRUE);** - Crea la política (para ver todo) con nombre ***todas_las_filas***, en la tabla ***facturas*** para el rol ***ventas***.
5. **create policy non_borrar on facturas as restrictive for delete to public using (finalizada=false)**; - Crea una política restrictiva.
## Comandos útiles:
- **sudo pluma /etc/postgresql/13/main/pg_hba.conf** - Entrar en el archivo de configuración.
- **sudo systemctl reload postgresql** - Reinicia el servidor de postgres.
- **show search_path** - Da el esquema en el que se van a buscar las tablas.
- **alter user u3 in database dam set search_path to es3;** - Cambia el path para que busque los esquemas por **_es3_** u no por el nombre de usuario **_u3_**, para que funcione correctamente el comando \dt. (Salir y entra de nuevo al usuario para que se aplique).
- **select * from pg_tables where tableowner='u3';** - Muestra información de las tablas de usuarios. 
