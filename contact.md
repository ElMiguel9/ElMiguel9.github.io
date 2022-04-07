---
layout: default
title: Lenguajes web semántica
---
<div id="contact">
  <h1 class="pageTitle">Lenguajes de la web semántica</h1>
  <p class="intro"></p>
</div>


<!--
<div id="contact">
  <h1 class="pageTitle">Contact Me</h1>
  <div class="contactContent">
    <p class="intro">This is an example Contact page. If you want to make changes then do so in the <code>contact.html</code> file.</p>
    <p>The form is provided by <a href="http://formspree.io/">Formspree.</a> Follow the directions on their site to set up the form for use.</p>
    <p>If you have questions about the theme feel free to <a href="mailto:brimaidesigns@gmail.com">email me</a> or create an issue on <a href="https://github.com/brianmaierjr/long-haul">GitHub</a>. Enjoy!</p>
  </div>

  <form action="http://formspree.io/your@mail.com" method="POST">
    <label for="name">Name</label>
    <input type="text" id="name" name="name" class="full-width"><br>
    <label for="email">Email Address</label>
    <input type="email" id="email" name="_replyto" class="full-width"><br>
    <label for="message">Message</label>
    <textarea name="message" id="message" cols="30" rows="10" class="full-width"></textarea><br>
    <input type="submit" value="Send" class="button">
  </form>
</div-->
<h1>"TEORIA"</h1>
<p>DISEÑO FÍSICO
Objetivos del diseño físico:
Mejorar el rendimiento.
Escalabilidad.
Disponibilidad / Integridad.
Facilidad de administración.
Integridad
Estructuras físicas y lógicas:
Estructuras lógicas: jerarquía formada por tablespaces, segmentos, extensiones y bloques
Estructuras físicas: archivos de datos que forman los tablespaces
Estados bases de datos:
OPEN: Funcionamiento completo
MOUNT: Permite recuperar BBDD. Añadir, quitar o renombrar archivos log. Cambio de modo ARCHIVELOG. 
NOMOUNT: Permite modificar BBDD. Archivos de parámetros init.ora. Crear, borrrar y modificar.
SHUTDOWN: No accesible
Tipos de vistas a los datos:
USER: Objetos del usuario.
ALL: Objetos con permisos.
DBA: Todos los objetos de la base de datos.
TABLESPACES
TABLESPACE: Espacio lógico de almacenamiento de datos.
Tipos de TABLESPACE:
SYSTEM: Almacena.
TEMP: Datos temporales.
UNDO: Información transaccional.
DATA: Datos de la aplicación.
Estados de TABLESPACE:
En línea: a disposición de las aplicaciones y BDs
Desconectado: datos no disponibles.
Parámetros de almacenamiento:
DBA_TABLESPACES
USER_TABLESPACES (select * from user_tablespaces;)

Sintaxis TABLESPACE:
BIGFILE o SMALLFILE para grandes o pequeños tablespaces del orden de Terabytes.
Por defecto smallfile, como máximo 32 Gb. Máximo incremento es 5 bloques.
DATAFILE para datos de la aplicación.
TEMPFILE para datos temporales. Se debe tener al menos un tablespace por esquema y un temporal para todos los esquemas, salvo usos masivos del temporal, entonces se crea un temporal para el esquema oportuno.
UNDO TABLESPACE:	
Aunque puede haber más de un tablespace de undo por instancia, sólo uno está activo. Los segmentos undo crecen o disminuyen de acuerdo a las necesidades de las transiciones.
Se utiliza para:
Rollback de transiciones.
Lectura consistente.
Operaciones de recuperación de bases de datos.
Funcionalidad de Flashback.

Ejemplos de TABLESPACES:

CREATE SMALLFILE			# El TABLESPACE es menor de 32Gb
	TABLESPACE “PRUEBA”	# El TABLESPACE se llama PRUEBA
	LOGGING			# redolog, no se. Ponlo siempre que haya un CREATE.
	DATAFILE ‘C:\Path\PRUEBA.ora’ 		#Ruta TABLESPACE
SIZE 5M AUTOEXTEND		# Tamaño TABLESPACE = 5Mb
	ON NEXT 1M MAXSIZE 10M		# Crece de 1 en 1 Mb hasta 10Mb como máximo
	EXTENT MANAGEMENT LOCAL	# Se guarda en local
	SEGMENT SPACE MANAGEMENT AUTO	#Lo gestiona oracle automáticamente


DROP TABLESPACE prueba			#Borrar el TABLESPACE
INCLUDING CONTENTS and datafiles	#si no tiene objetos no hace falta y borra el fichero del sistema operativo
CASCADE CONSTRAINTS			# Borra todas las restricciones de clave ajena

CREATE SMALLFILE
TABLESPACE TEMPRUEBA
TEMPFILE ‘C:/path/TEMPRUEBA.ova
SIZE 2M 
BLOCKSIZE 2K						#2K tamaño de bloque
REUSE AUTOEXTEND ON NEXT 640K MAXSIZE 32M 	#Gestiona oracle
EXTENT MANAGEMENT LOCAL UNIFORM SIZE 1024K	# de 1024 en 1024 Kb

DROP TABLESPACE TEMPPRUEBA
INCLUDING CONTENTS and datafiles
CASCADE CONSTRAINTS

alter system set db_2k_cache_size = 1M scope=both;
create smallfile tablespace small_tb
logging
datafile '/opt/oracle/oradata/ORCLCDB/stb.dbf'
size 4M
blocksize 2K
extent management local uniform size 10K
segment space management auto;

Ver características:

select block_size, initial_extent, next_extent, min_extents, max_extents,
max_size, pct_increase, min_extlen, extent_management, allocation_type,
segment_space_management
from user_tablespaces
where tablespace_name = 'SMALL_TB';

SEGMENTOS
EXTENSIONES: Conjunto de bloques de datos contiguos de un TABLESPACE.
Cuando se elimina un segmento, sus extensiones se liberan.

SEGMENTOS: Conjunto de extensiones dedicadas a un objeto de la BD y almacenada en un fichero. El espacio que utiliza lo determina:
El objeto creado (tabla, índice, cluster, undo segment).
Los parámetros de almacenamiento predeterminados del TABLESPACE en el que se almacena.

Parámetros de almacenamiento de SEGMENTO:
INITIAL: Tamaño inicial de la extensión.
NEXT: Tamaño de la siguiente extensión.
PCTINCREASE: Factor de incremento geométrico para sucesivas extensiones.
MAXEXTENTS: Número máximo de extensiones.
MINEXTENTS: Número mínimo de extensiones.
Fórmulas:
Tamaño siguiente extensión oracle: NEXT*(1+(PCTINCREASE/100))
Ejemplo: NEXT:4Mb y PCTINCREASE: 50%.
1ª extensión = INITIAL
2ª extensión = NEXT = 4Mb
3ª extensión = 4Mb*(1+50/100)= 6Mb
4ª extensión = 6Mb*(1+50/100)= 9Mb

Tipos de SEGMENTO:
TABLE, Segmento de datos: extensiones asignadas a una tabla. No se pueden compartir segmentos en tablas distintas. 
Al menos una extensión.
Su espacio se libera eliminándola.
ALTERTABLE modifica el storage.
MOVE la cambia de TABLESPACE.
PCTFREE reserva en el interior de cada bloque un porcentaje de espacio para actualizaciones de datos.
INDEX, Segmentos de índices: datos sobre índices.
Al menos una extensión.
Su espacio se libera eliminando o borrando las tablas o clusters indexados.
Se aconseja guardar los índices en diferente TABLESPACE que las tablas para eliminar contiendas.
ALTER INDEX REBUILD para modificar configuración del storage y tablespace de un índice.
ROLLBACK, Segmentos de UNDO: uno o varios segmentos con información para deshacer transacciones.
TEMPORARY, Segmentos temporales: para procesar consultas cuando no hay espacio suficiente. Almacenan datos temporales durante operaciones. Cada usuario tiene la suya (create user) o una para la TABLESPACE para cada BD y todos los usuarios.
CLUSTER

Ejemplos de SEGMENTOS

CREATE TABLE ejemplo (
 cod NUMBER(2),
 nombre VARCHAR2(14) )

 STORAGE (INITIAL 100K NEXT 50K MINEXTENTS 1 MAXEXTENTS 50
 PCTINCREASE 5); 	#Atributos de los segmentos

SELECT * FROM USER_SEGMENTS;	#Muestra los atributos y valores

COMENTARIOS
A partir de la 3ª extensión se incrementa un 5% el espacio de la extensión anterior:
52,5 K  → 52 si el tamaño de bloque fuera 2K

Crear tabla EJEMPLO y asignarle política de crecimiento de extensiones que consuma lo menor posible:

create table ejemplo (
id number(4) not null,
 txt char(600) not null
)
storage (initial 2K next 2K minextents 1)
tablespace small_tb ;

Ver sus características relativas al almacenamiento consultando la vista user_tables

SQL> select initial_extent, min_extents, max_extents, pct_free, pct_used
from user_tables where table_name = 'EJEMPLO';




Consultar también las características del segmento en el que se sitúa la tabla utilizando la vista user_segments:

SQL> select segment_name, segment_type, bytes, blocks, initial_extent,
next_extent, tablespace_name from user_segments where segment_name like
'%EJEMPLO%';



BLOQUES
BLOQUE: Unidad mínima de transferencia de información.
Su tamaño se define al crear la BD. Debe ser múltiplo del tamaño de bloque del S.O. del servidor (entre 2 Kb y 32Kb).
DB_BLOCK_SIZE, tamaño del bloque.
Parámetros de configuración de BLOQUES en TABLAS e ÍNDICES:
PCTFREE int, mínimo porcentaje de reserva para actualizaciones, 10% por defecto.
PCTUSED int, mínimo porcentaje ocupado para no admitir más inserciones, 40% por defecto.
INITRANS int
MAXTRANS int
cláusula_almacenamiento
FREELIST, lista de bloques disponibles para insertar.

Ejemplo BLOQUES y FÓRMULAS.

Tabla: 

Nº Estimado TUPLAS: 3000
INITRANS = 1
MAXTRANS = 20
Tamaño bloque = 4 Kb = 4096 bytes
PCTFREE = 10
PCTUSED = 85

Tamaño cabecera = 61+(INITRANS*23) = 61+23=84 bytes
Si es un índice o cluster: 61+(INITRANS*46)
Tamaño Espacio Útil: Tamaño Bloque - Tamaño cabecera = 4096 - 84 = 4012 bytes
Tamaño Espacio PCTFREE = TamEspacioUtil * PCTFREE = 4012*10%=401 bytes
Espacio Disponible Insertar = TamEspacioUtil - TamEspacioPCTFREE = 4012 - 401 = 3611 bytes
Tamaño Fila = Cabecera + 1*atributo corto o 3*atributo largo + LongMediaFila = 3 + 3*1 +16 = 22 bytes
LongMediaFila= SUMA(longmediaColumn) = probNoNulo(atrib1)*TamañoEfectivo(atr1)+... = 100%*4 + 90%*10 + 10%*30 = 4 + 9 + 3 = 16 bytes
Nº Filas Bloque = TRUNC(EspacioDisponibleInsertar / TamFila) = TRUNC(3611/22) = 164 filas
Nº Bloques = Nº Estimado duplas / Nº Filas Bloque = 3000/164 = 18,3 → 19 bloques
Tamaño tabla = Nº Bloques * Tamaño Bloque = 19 * 4096 = 76 Kb

ÍNDICES

INDICE: Estructura auxiliar para mejorar el tiempo de búsqueda. WHERE = condición. Reduce el tiempo de las operaciones de combinación de tablas. Mejora las consultas que requieren agrupación u ordenación (ORDER BY, GROUP BY, DISTINCT).
Tipos de ÍNDICES:
Árbol-B+, el más habitual
Mapa de Bits
Hashing
Índices de palabras

Índices de ORACLE:



CLUSTERS
CLUSTER: Grupos de una o más tablas. Las filas se guardan físicamente juntas. Comparten una o más columnas, clave del cluster.
Se usan en tablas con JOINS o relación maestro detalle. La clave solo se almacena una vez. No usar si:
Valor clave se modifica a menudo
En tablas que se recorren completas a menudo
Si las filas que se agrupan juntas varían en número o superan el tamaño de uno o dos bloques.

CONCURRENCIA Y CONSISTENCIA
COMMIT: Se guardan en la base de datos los cambios realizados.
ROLLBACK: Se restaura la versión de la BBDD donde se estaba trabajando al principio.

Niveles de aislamiento de ISO:


Por defecto el nivel de aislamiento es read commited.
En ORACLE SOLO EXISTEN: Read commited, Serializable y solo lectura

Técnicas de bloqueo, Protocolos:
Wait-die: Fuerza una transacción a esperar en caso de que entre en conflicto con otra transacción cuya marca de tiempo sea más reciente, o a abortar si la transacción que se está ejecutando es más antigua. Familia educada: El abuelo espera a que el nieto termine de hablar y el nieto se retira si el abuelo está hablando (pero conserva su TS).

Wound-wait: que permite a una transacción matar a otra que posea una marca de tiempo más reciente, o que fuerza a la transacción peticionaria a esperar.
Abuelo intransigente: El abuelo aparta al nieto cuando él quiere hablar y el nieto espera a que el abuelo termine de hablar para hacerlo él.

TRANSACCIÓN: secuencia de operaciones que han de ejecutarse en conjunto.
Propiedades ACID:
Atomicity: No puede haber ejecución parcial y el usuario no se preocupa por ello
Consistency: la BD pasa a un estado consistente

RECUPERACIÓN ANTE FALLOS

RECUPERACIÓN: Un sistema de recuperación consiste en restaurar la BD a un estado que se sepa correcto, tras cualquier fallo que la haya dejado en un estado incorrecto. En Oracle hay recuperación automática ante fallos, el proceso varía dependiendo del tipo de fallo y las estructuras afectadas.

Estructuras de recuperación:
Ficheros Redo Log: Ficheros de almacenamiento de cambios en la BD (Recovery)
Se almacenan los cambios confirmados y no confirmados y actualiza la BD a partir del fallo. Dos partes:
Online redolog o online redo log: Al menos dos ficheros. Proceso LGWR. Cada registro contiene el valor antiguo y el nuevo.
Archived redolog o archive redo log: almacenaje de online redo log en ficheros antes de reutilización. (ARCHIVELOG o NOARCHIVELOG)
Ficheros de Control: Almacenan el estado de la estructura física de la BD. Guían la recuperación.
Rollback Segments: Almacenan las últimas sentencias realizadas sobre la BD. Saben cuando se ha confirmado o no una transacción.
Backups de la BD: Copias de seguridad (Restoring).

1).- Recuperación de Emergencia: Copias de seguridad y fichero Log
2).- Recuperación en caliente: Fichero Log para deshacer y rehacer Transacciones.
3) - Rollback de la Transacción.
4).- Recuperación en frío: Sólo copia de Seguridad con pérdida de Últimas Transacciones
Métodos de recuperación. ORACLE: Sin archivado o con archivado. Completo o incompleto.
La utilización de una copia de backup de ficheros de datos siempre necesita de una recuperación física. Prerrequisito = ARCHIVELOG, ARCHIVE LOG.
Opciones de recuperación:
Recuperación BD entera.
Recuperación de un tablespace mientras resto BD abierta.
Recuperación de un fichero de datos mientras resto BD abierta.
Cómo arrancar una BD: Tres pasos y combina la creación de una instancia y el arranque de la BD:
Instancia: connect internal, startup nomount.
Montar una BD: alter database mount.
Abrir una BD: alter database open.
Alternativas de arranque:startup, solo para ABD: startup restrict o arranque forzado: startup foce.
Existen 3 modos de parar una BD en ORACLE:
Normal: shutdown. Espera por los usuarios conectados. No permite nuevas conexiones. No se efectúa recuperación durante el posterior arranque.
Inmediata: shutdown inmediate. Las conexiones actuales se completan. No se efectúa recuperación durante el posterior arranque.
Abortando: shutdown abort. Parada drástica, no espera a que las conexiones actuales se completen. Es necesaria una recuperación durante el posterior arranque.

Comando de recuperación:
	RECOVER [AUTOMATIC] [FROM “localizacion”][BD]
[ UNTIL CANCEL]
[UNTIL TIME fecha]
[UNTIL CHANGE entero]
[USING BACKUP CONTROLFILE]


 </p>

<h2>"preguntas"</h2>
<p> Q1
1.- Afirmación correcta:
Los ficheros de datos (datafiles) son estructuras físicas de las bases de datos Oracle, mientras que los espacios de tablas (tablespaces), segmentos, extensiones y bloques de datos son estructuras lógicas✅

Los ficheros de datos (datafiles) son estructuras físicas de las bases de datos Oracle, mientras que los espacios de tablas (tablespaces), segmentos, extensiones y bloques de datos son estructuras lógicas❌

Los ficheros de datos (datafiles) son estructuras físicas de las bases de datos Oracle, mientras que los espacios de tablas (tablespaces), segmentos, extensiones y bloques de datos son estructuras lógicas❌

Los ficheros de datos (datafiles) son estructuras físicas de las bases de datos Oracle, mientras que los espacios de tablas (tablespaces), segmentos, extensiones y bloques de datos son estructuras lógicas❌

2.-  Supongamos que la tabla PRUEBA se creó con la siguiente sentencia SQL y el tamaño de bloque definido en Oracle es 2K. ¿Cuáles son los tamaños de las tres primeras extensiones para la tabla?
CREATE TABLE prueba ( 
    Field1 VARCHAR2(5), 
    Field2 VARCHAR2(20)) 
    STORAGE (INITIAL 30K NEXT 20K MINEXTENTS 1 MAXEXTENTS 2 PCTINCREASE 10);

Ninguna de las anteriores ✅
 20K, 22K y 24.22K❌
 30K, 20K y 22K❌
 20K, 22K y 24K❌

3.- Cuando una instancia está en estado mounted, la instancia se encuentra vinculada con la base de datos residente en el disco y solamente se permiten conexiones de usuarios que tengan los privilegios de SYSDBA

Verdadero✅
Falso❌

4.- ¿Qué es un tablespace?

 Ninguna de las anteriores es correcta❌
 Es un espacio asignado dentro de una tabla❌
 Es una tabla que indica el espacio libre de la base de datos ❌
 Es uno de los componentes de almacenamiento lógico de la base de datos ✅
5.- ¿Cuántas instancias de una base de datos pueden estar simultáneamente activas?

Varias compartiendo el acceso a la organización física que almacena los datos ✅
 Varias pero solo una por cada nodo de la red❌
 Solo una en cualquier momento❌
 Solo dos, una activa y la otra en modo pasivo como respaldo❌
Q3
1.- En la siguiente transacción se conserva el registro del empleado insertado

INSERT INTO empleado VALUES (empleado_id, ...);
SAVEPOINT P1;
DELETE FROM empleado WHERE empno = empleado_id; 
ROLLBACK;

Verdadero❌
Falso✅


2.- En el SGBD Oracle 19c, el nivel de aislamiento por defecto es:

Read committed ✅
 Read uncommited❌
 Serializable❌
 Repeatable read❌

3.- Relacionado con Concurrencia de la BBDD, ¿cuál de las siguientes afirmaciones es cierta?:
Un usuario nunca puede abrir más de una sesión de forma concurrente en la misma instancia❌
 Las operaciones realizadas por un usuario no son visibles al resto de los usuarios hasta que se realiza un SAVEPOINT (automático o manual) en su sesión❌
 Las operaciones realizadas por un usuario no son visibles al resto de los usuarios hasta que se realiza un COMMIT (automático o manual) en su sesión ✅
 Las operaciones realizadas por un usuario no son visibles al resto de los usuarios hasta que se realiza un ROLLBACK (automático o manual) en su sesión❌

4.- El protocolo Wait-Die para control del acceso concurrente a recursos se basa en un semáforo que se activa (se pone en rojo) cuando una transacción accede a un recurso común, obligando a que todas las demás transacciones que desean acceder al recurso esperen hasta que ella finalice (muera) y abandone el sistema.
Verdadero❌
Falso✅



5.-Si en un Oracle 19c ejecutamos la siguiente instrucción:
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ; 

¿Qué tipo de errores de lectura estamos permitiendo a cambio de aumentar el nivel de concurrencia?

Permite lecturas fantasmas, pero no lecturas sucias o no repetibles ❌
 Permite lectura sucias, lecturas no repetibles y lecturas fantasmas❌
 Ninguna de las anteriores respuestas es correcta✅
 Permite lecturas sucias, lecturas no repetibles, pero no lecturas fantasma❌
 No permite ningún tipo de problemas de lecturas❌
Q4
1.- Selecciona la vista que es necesario utilizar en la siguiente sentencia SQL para obtener la información interna de Oracle sobre los bufferes de redolog:

SELECT * FROM V$LOG ✅

2.- En una situación donde se ha producido un fallo del sistema que no ha producido daños en la BD pero no se tiene garantía del estado de las últimas transacciones
¿qué se utilizaría en el proceso de recuperación?.

 Sólo copia de seguridad con pérdida de últimas transacciones.❌
 Copias de seguridad y fichero log .❌
 Rollback de las transacciones.❌
 Fichero log para deshacer y rehacer transacciones.✅

3.- Para pasar una base de datos Oracle a modo ARCHIVELOG hay que realizar los siguientes pasos:
1 	shutdown immediate;
2 	alter database archivelog;
3 	alter database open;
Verdadero❌
Falso✅

4.- Si queremos utilizar el Data Recovery Advisor de RMAN (Recovery Manager) para corregir errores en una base de datos debemos realizar la siguiente secuencia de órdenes:
$ rman target /
RMAN> list failure;
RMAN> advise failure all;
RMAN> change failure all;
RMAN> exit;

Verdadero✅
Falso❌

5.- Indica la función principal del proceso PMON de una instancia de Oracle:
Restaura las transacciones no validadas de los procesos de usuario que abortan.✅
 Ninguna de las anteriores. ❌
 Guía la recuperación de la instancia de la base de datos en caso de fallo local.❌
 Guía la recuperación del contenido de los buffers de datos y la caché del diccionario en caso de fallo del sistema.❌
</p>