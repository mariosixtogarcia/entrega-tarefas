# Apuntes
## A sublinguaxe DDL
### Índice

- [Simboloxía](#antes-de-empezar-aclarar-o-significado-da-simboloxía)
- [Sentencia CREATE](#uso-da-sentencia-create)
- [Restricións](#Uso-das-restricións)
- [Creación dunha base de datos](#creación-dunha-base-de-datos)
- [Sentencia ALTER](#uso-da-sentencia-alter)
- [Sentencia DROP](#uso-da-sentencia-drop)

O DDL é unha sublinguaxe de SQL, xunto co DDL, DCL, TCL, SCL. O DDL ten a funcionalidade de **crear**, **borrar** e **modificar** tablas da BD. Este sublinguaxe actúa sobre os **obxectos**  significa Data Definition Languaje. Esto serve para crear, modificar e borrar tablas e bases de datos

  ### Antes de empezar, aclarar o significado da simboloxía:

  [ ] Os corchetes significan que o que está entre eles é opcional.
 
   |  A barra vertical significa OU (o que está antes ou o que está despois).

### Uso da sentencia CREATE
A sentencia CREATE, serve para crear tablas, BD, usuarios, esquemas (é o mismo que as BD, pero con menos restriccións).
```sql
CREATE (DATABASE | TABLE | SCHEMA | USER)
[IF NOT EXISTS] <nome_da_BD>
[CHARACTER SET <nome>]
(<atributo1> <dominio1> [NOT NULL] [DEFAULT <x>],
[restriction1],
...
[restriction2]
);
```
O ```IF NOT EXISTS``` serve para que comprobe se existe algunha táboa, bases de datos ou usuarios co nome proporcionado.

O ```CHARACTER``` Usase para asignar xogos de caracteres a BD, como o UTF.

O ```DEFAULT``` asigna un valor por defecto.


### Uso das restricións ###
As restriccións que aparecen ao final en realidade chámanse *constraints*, que é o nome da sentencia que usamos para definilas.

Existen 4 *contriants* ou restriccións, que son as seguintes:

**De clave primaria**

```sql
[[CONSTRAINT <nome_da_restricción>] 
	 PRIMARY KEY (<atributos>)]
```

**De clave foránea**
```sql
[[CONSTRAINT <nome_da_restricción>]
         FOREIGN KEY (<atributos>)
         REFERENCES <nome_da_táboa_de_referencia> (<atributo_referenciado>)]
 ```
 
**De unicidade**
```sql
[CONSTRAINT <nome_da_restricción>]
             UNIQUE (<atributos>)]
```
Coa sentencia ``` UNIQUE + NOT NULL``` crearemos as claves alternativas

**De comprobación** (non entra en exame)
```sql
[CONSTRAINT <nome_da_restricción]
	CHECK (atributoA IN (valor1, ... , valorN))
	[[NOT] DEFERABLE]
	[INITIALLY INMEDIATED | DEFERRD]]
```

#### Creación dunha base de datos ####
Para crear unha base de datos, temos que levar a cabo diferentes CREATE.

O primeiro que temos que usar é:

```sql
CREATE SCHEMA <nome_da_base_de_datos>
```

Logo desto temos que crear os dominios, para iso usaremos:

```sql
CREATE DOMAIN <nome_do_dominio> <tipo_de_datos>
```

Esta creación de dominios serve para declarar un elemento que vamos usar máis de unha vez. Esto aforrarános traballo xa que non teremos que escribir todo, (a sentenncia, o nome e o tipo (xunto co número da extensión)).

Dende que xa temos a BD creada, e todos os dominios declarados, comezaremos a crear táboas con:

```sql
CREATE TABLE <nome_da_táboa>
	<atributo1> <dominio1> [NOT NULL] [DEFAULT <x>]
````
	
**Un exemplo de creación de unha BD dende cero**

```sql
CREATE SCHEMA BaseDeDatosClase09032020

CREATE DOMAIN Nome_Válido CHAR(30);
CREATE DOMAIN Tipo_Código CHAR(5);
CREATE DOMAIN Tipo_DNI CHAR(9);

CREATE TABLE Sede (
    Nome_Sede Nome_Válido,
    Campus    Nome_Válido NOT NULL,
    CONSTRAINT PK_Sede  -- Isto usase para nomear onde se declarou a PRIMARY KEY, por se algún día é necesario buscalo --
        PRIMARY KEY (Nome_Sede)
);

CREATE TABLE Departamento (
    Nome_Departamento Nome_Válido,
    Teléfono          CHAR(9) NOT NULL,
    Director          Tipo_DNI
        PRIMARY KEY (Nome_Sede)
);

CREATE TABLE Ubicación (
    Nome_Sede         Nome_Válido,
    Nome_Departamento Nome_Válido,
    CONSTRAINT PK_Ubicación
        PRIMARY KEY (Nome_Sede, Nome_Departamento),
    CONSTRAINT FK_Sede
        FOREIGN KEY (Nome_Sede)
        REFERENCES Sede (Nome_Sede)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    CONSTRAINT FK_Departamento
        FOREIGN KEY (Nome_Departamento)
        REFERENCES Departamento (Nome_Departamento)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
);

CREATE TABLE Grupo (
    Nome_Grupo        Nome_Válido,
    Nome_Departamento Nome_Válido,
    Área              Nome_Válido NOT NULL,
    Lider             Tipo_DNI,
    PRIMARY KEY (Nome_Grupo, Nome_Departamento)
    FOREIGN KEY (Nome_Departamento) REFERENCES Departamento
        ON DELETE CASCADE
        ON UPDATE CASCADE

);

CREATE TABLE Profesor (
    DNI           Tipo_DNI    PRIMARY KEY,
    Nome_Profesor Nome_Válido NOT NULL,
    Titulación    VARCHAR(20) NOT NULL,
    Experiencia   Integer,
    N_Grupo         Nome_Válido,
    N_Departamento  Nome_Válido,
    CONSTRAINT FK_Grupo  
        FOREIGN KEY        (N_Grupo, N_Departamento)
        REFERENCES N_Grupo (Nome_Grupo, Nome_Departamento)
        ON DELETE SET NULL
        ON UPDATE CASCADE
);

CREATE TABLE Proxecto(
    Código_Proxecto Tipo_Código,
    Nome_Proxecto   Nome_Válido NOT NULL,
    Orzamento       MONEY       NOT NULL,
    Data_Inicio     DATE        NOT NULL,
    Data_Fin        DATE,
    N_Gr            Nome_Válido,
    N_Dep           Nome_Válido,
    UNIQUE (Nome_Proxecto),
    CONSTRAINT Check_Dates
        CHECK (Data_Inicio < Data_Fin),
    CONSTRAINT FK_Grupo
        FOREIGN KEY        (N_Gr, N_Dep)
        REFERENCES N_Grupo (Nome_Grupo, Nome_Departamento)
        ON DELETE SET NULL
        ON UPDATE CASCADE
);

ALTER TABLE Departamento
    ADD CONSTRAINT FK_Departamento_Profesor
        FOREIGN KEY (Director) 
        REFERENCES Profesor (DNI) 
        ON DELETE SET NULL
        ON UPDATE CASCADE;
        
CREATE TABLE Participa (
    DNI             Tipo_DNI,
    Código_Proxecto Tipo_Código,
    Data_Inicio     DATE    NOT NULL,
    Data_Cese       DATE,
    Dedicación      INTEGER NOT NULL,
    PRIMARY KEY (DNI, Código_Proxecto)
    CHECK (Data_Inicio < Data_Cese)
    CONSTRAINT FK_Participa
        FOREIGN KEY (DNI)
            REFERENCES Profesor (DNI)
            ON DELETE NO ACTION
            ON UPDATE CASCADE
        FOREIGN KEY (Código_Proxecto)
            REFERENCES Profesor (Código_Proxecto)
            ON DELETE NO ACTION
            ON UPDATE CASCADE
        
);
```
Nesta BD, vemos como ao principio creamos a BD con ```CREATE SCHEMA ``` , logo definimos todos os dominios con ```CREATE DOMAIN ```. Vemos que o Tipo_DNI ten CHAR (9), eso significa que a súa extensión sempre vai ter que ser 9, xa que o DNI son 8num + 1 letra
Lodo disto comezamos a crear táboas con ```CREATE TABLE ``` Creamos as táboas: **SEDE**, **DEPARTAMENTO**, **UBICACIÓN**, **GRUPO**, **PROFESOR**, **PROXECTO** e **PARTICIPA**. En cada unha delas levase a acabo unha cousa distinta. Por exemplo: Na táboa Sede, faise unha restricción / constraint, e dise que a clave principal ```PRIMARY KEY``` será Nome_Sede, esta nunca pode ser nula, nin repetirse os valores (existiría conflicto). Na táboa Ubicación faise unha cable composta, por: Nome_Sede e Nome_Departamento. Támen podemos observar a sentencia ```FOREIGN KEY```, sirve para crear relacións entre táboas, isto fai referencia a unha clave principal de outra táboa.

Logo na BD de exemplo encontramos os borrados e modificacions ```ON DELETE``` e ```ON UPDATE```, que pode ser: en *Cascada* identificado cunha C, que como ben dice, actuaría en cascada tanto o borrado como ás modificacións. o *No Action*, que se identifica cunha R, non afectaría en nada nin ao borrado nin ás modificacións. *Set Null*, aplica ante borrado ou modificación un valor nulo, e por último *Set Default* aplicaría un valor por defecto.

Máis tarde na táboa Proxecto, observamos o primeiro ```CHECK``` que comprobará unha comparación entre datas neste caso (Data_Inicio anterior a Data_Fin, se non é así, non aceptará datos que non cumpran iso)

### Uso da sentencia Alter ###
```sql
ALTER TABLE [IF EXISTS] <nome_da_táboa>
	    [RENAME TO <novo_nome_da_táboa>],
	    [RENAME [COLUMN | CONSTRAINT] <nome_orixinal> TO <novo_nome>],
	    [SET SCHEMA <novo_Schema>],
	    [ADD | DROP [COLUMN | CONSTRAINT] <nome>]
```
O ```ALTER TABLE``` usase para modificar datos das táboas. Tanto para engadir como para eliminar columnas ou restriccións.Tamén podemos renomear algunhos elementos e vincular táboas con outras bases de datos con el.

### Uso da sentencia Drop ###
Esta é outra maneira de eliminar datos da táboa. Esta sentencia ademáis de eliminar a táboa tamén o contido.
Esta é unha forma perfecta para eliminar a BD completa

```sql
DROP SCHEMA [IF EXISTS] <nomeDoSchema>
	    [RESTRICT | CASCADE]
;

DROP TABLE  [IF EXISTS] <nomeDaTaboa>
	    [RESTRICT | CASCADE]
;
```

# Apuntes
## A sublinguaxe DML
### Índice

- [Uso da sentencia INSERT](#Uso-da-sentencia-INSERT)
- [Uso da sentencia UPDATE](#Uso-da-sentencia-UPDATE)
- [Uso da sentencia DELETE](#Uso-da-sentencia-DELETE)


O DML é outra sublinguaxe de SQL, que ten a funcionalidade de **agregar**, **borrar** ou **modificar** tuplas de unha táboa da BD. Esta sublinguaxe a diferencia de DDL, ***actúa sobre os datos***, contando que a anterior actuaba sobre os obxectos.
O significado de DML e *Data Manipulation Languaje*, en galego: *Linguaxe de manipulación de datos*.

  ### Antes de empezar, aclarar o significado da simboloxía:

  [ ] Os corchetes significan que o que está entre eles é opcional
 
   |  A barra vertical significa OU (o que está antesd ou o que está despois)


### Uso da sentencia INSERT
  A sentencia insert, como ben dice en inglés, **inserta** datos da táboa.
```sql
INSERT INTO <nome_da_táboa>
[(atributo1, atributo2, ...)]
VALUES
(<valor1A>, <valor2A>, ...),
(<valor1B>, <valor2B>, ...),
| SELECT <atributo1> FROM <tabla1>
```
#### Exemplos
```sql
INSERT INTO world
[(name, continent, area)]
VALUES
('Spain', 'Europe', 100),
('Portugal', 'Europe', 10);
```
Neste primeiro exemplo estamos engadindo 2 tuplas seguidas a táboa world. Como ben vemos podemos engadir todas as tuplas que queiramos de un tirón, separando sempre con , (coma) **E ao final acabando con ;**.
```sql
INSERT INTO world
[(name, continent, area)]

SELECT nome, continente, area
FROM mundo
WHERE continente='Europa';
```
Como tamén vemos neste outro exemplo, podemos combinar o **INSERT INTO** con SELECT, FROM e WHERE.

### Uso da sentencia UPDATE
  A sentencia update, serve para modificar os datos xa existentes da táboa.
 ```sql
 UPDATE <nome_da_táboa>
 SET <atributo1> = <valor1>,
     <atributo2> = <valor2>,
 [WHERE <predicado>];
 ```
 Esta última liña ```[WHERE <predicado>] ``` é totalmente opcional
 
 #### Exemplos
 ```sql
 UPDATE world
 SET continent = 'Asia';
 WHERE name = 'Spain'
    OR name = 'Portugal'
 ```
 Neste exemplo añadiremos onde se encontre o nome Spain e Portugal o novo valor Asia.
 É moi importante puntualizar que se non establecemos o WHERE, aplicariao en toda a BD.
 
 ### Uso da sentencia DELETE
  A sentencia delete, serve para eliminar valores de algunhas tuplas, que lle digamos, unha táboa, ou ata poderiamos eliminar a BD enteira.
 ```sql
 DELETE FROM <nome_da_táboa>;
 [WHERE <predicados>]
 ```
 Como ben aparece na fórmula, o WHERE é opcional, aínda que non debería selo. Xa que se non espeficias o WHERE eliminarías a BD enteira. E ao mellor eso non é o que queremos, como un amigo do profesor, nun dos seus traballos, jejejejeje.
 
 #### Exemplos
 ```sql
 DELETE FROM world
 WHERE continent = 'Europe';
 ```
 Neste exemplo, eliminariamos todas as tuplas na que existise un valor de continent, que sexa iguala Europe.
