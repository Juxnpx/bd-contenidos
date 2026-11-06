# Ejercicios de Modelo Relacional

## Ejercicio 1 - Repaso

Considera las siguientes relaciones:

$EMPLEADO$(<u>codigo</u>,nombre,despacho\*)
Clave externa: despacho -> DESPACHO.codigo

| codigo | nombre | despacho |
| ------ | ------ | -------- |
| E1     | María  | 37       |
| E2     | Pedro  | 37       |
| E3     | Flor   |          |
| E4     | Juan   | 40       | 


```sql
CREATE TABLE EMPLEADO(
	codigo TEXT PRIMARY KEY,
	nombre TEXT NOt NULL,
	despacho TEXT UNIQUE,
	FOREIGN KEY (despacho) REFERENCES DESPACHO(codigo) ON UPDATE CASCADE ON DELETE SET NULL
) STRICT;

CREATE TABLE DESPACHO(
	codigo TEXT PRIMARY KEY,
	pabellon TEXT NOT NULL,
	planta INTEGER CHECK (planta >0 and planta<10)
)STRICT;
```

$DESPACHO$(<u>codigo</u>,pabellon,planta)

| codigo   | pabellon    | planta |
| -------- | ----------- | ------ |
| 37       | central     | 2      |
| 40       | informática | 4      |
| 25       | central     | 2      | 

### Preguntas

1. Indica cuál es el grado y la cardinalidad de cada relación. Empleado=3 / Despacho=3 y la cardinalidad Empleado = 4 y Despacho = 3
2. Indica qué sucede si se intenta añadir una nueva tupla (E5, 1245, 25) a `EMPLEADO`. No encontramos con una restricción de semántica, porque no puede ser que un empleado se llame como un numero aunque el programa lo acepta porque el atributo es de tipo TEXT 
3. Indica qué sucede si se intenta borrar la tupla del empleado E4. Se borra correctamente eliminar tuplas de la tabla hija (referenciada), no viola restricciones de integridad referencial
4. Indica qué sucede si se intenta añadir una nueva tupla (E5, Elena, 30) a `EMPLEADO`. Fallo error en la clave ajena, el valor de despacho 30 no existe, viola la restriccion referencial
5. Indica qué sucede si se intenta modificar la PK del despacho 37 a el nuevo valor 50 en `DESPACHO`. ¿Cómo podríamos cambiar este comportamiento? Se modifica correctamente gracias a la funcion de UPDATE CASCADE, y cambia las tuplas tanto E1 y E2
6. Supón que la opción de borrado de la clave externa es borrado restringido. ¿Qué sucedería al intentar borrar el despacho 40 de `DESPACHO`? Borrado rechazado
7. Supón que la opción de borrado de la clave externa es borrado restringido. ¿Qué sucedería al intentar borrar el despacho 25 de `DESPACHO`? Se borra correctamente
8. Supón que la opción de borrado de la clave externa es borrado en cascada. ¿Qué sucedería al intentar borrar el despacho 37 de `DESPACHO`? Se borra el despacho 37 y los empleados E1 y E2
9. ¿Qué restricciones existen para añadir un nuevo despacho en DESPACHO?
	1. CLAVE->  codigo: debe ser unico y no nulo
	2. DOMINIO-> pabellon: No puede ser nulo
	3. DOMINIO->Planta: tiene que estar entre los valores declarados
10. ¿Se  puede borrar un empleado de la tabla `EMPLEADO` que tenga un despacho asignado? Si, un borrado en la tabla hija no influye 
11. ¿Existe alguna restricción para poder borrar una o varias filas de la tabla `EMPLEADO`? 
12. Define la asociación que existe entre `EMPLEADO` y `DESPACHO` según su cardinalidad y la opcionalidad de participación.
13. Escribe el código SQL de creación de estas relaciones tan completo como puedas. Asume aquello que consideres necesario y lógico.
14. Indica qué cambiaría en la definición en SQL si consideramos que los despachos son unipersonales (es decir que pertenezca a un solo empleado). Se haria poniendo unique a la columna de despacho de empleado

## Ejercicio 2 - Relaciones

Considere una relación `ESTUDIANTE` en una base de datos `UNIVERSIDAD` con los siguientes atributos (Nombre, NIF, TlfFamiliar, Dirección, TlfResidencia, Edad, NotaMedia). TlfFamiliar se refiere al de su casa familiar y TlfResidencia se refiere al de su residencia actual. Tenga en cuenta que el TlfFamiliar puede ser de un país diferente al TlfResidencia. A continuación se muestra una posible tupla de la relación:

(’Pedro Blas Fiz’, ‘123456789A’, ‘666111222, ‘Martinicos, 34’, ‘765432234, 19, 3.75)

1. Identifica la información crítica perdida de los atributos TlfFamiliar y TlfResidencia del ejemplo anterior. Prefijo del telefono de casa como el de residencia
2. ¿Almacenaría esta información adicional en los atributos TlfFamiliar y TlfResidencia o añadiría otros nuevos al esquema `ESTUDIANTE`? Se podria añadir otras atributos que solo almacenaran el prefijo pero si tuvieramos que hacer un busqueda por país, sino si solo lo queremos como el telefono como dato podemos añadir el prefijo en el atributo 
3. Considere el atributo Nombre. ¿Cuáles son las ventajas de dividir este valor en tres atributos (nombre, apellido1, apellido2)? Mejorar la busqueda, cumple la primera forma normal (valor atomico) 
4. ¿Cuál sería el criterio que recomendaría para decidir cuándo almacenar la información en un atributo único y cuándo dividirlo en varios?
5. Suponga que el estudiante puede tener entre ninguno y cinco teléfonos. Sugiera dos tipos de diseños diferentes que permitan este tipo de información. Tabla independiente de telefonos

## Ejercicio 3 - Claves externas

Considere las siguientes relaciones para una base de datos que controla las matriculaciones de los estudiantes en los cursos y los libros utilizados en los mismos: 

$ESTUDIANTE$(<u>dni</u>, nombre, asignatura, fechaNac) 

$CURSO$(<u>codigo</u>, nombre, departamento) 

$MATRICULACION$(<u>dni, codigoCurso, trimestre</u>, Nota) 

$LIBRO_USADO$(<u>codigoCurso, trimestre</u>, isbn) 

$LIBRO$(<u>isbn</u>, título, editorial, autor) 

**Especifique las claves externas necesarias** para este esquema, argumentando todas sus decisiones. 

LibroUsado- >Libro(isbn) / codigoCurso(codigo)
Matriculacion-> estudiante(dni) /  codigoCurso->curso(codigo)

## Ejercicio 4 - De SQL a relaciones

A partir de las sentencias SQL siguientes, define las relaciones en el modelo relacional de forma exhaustiva indicando todo lo que puedas de las mismas.

```sql
-- Tabla de Bandas de Rock
CREATE TABLE bandas (
    id_banda INT PRIMARY KEY,
    nombre_banda VARCHAR(100) NOT NULL,
    ano_formacion INT NOT NULL,
    pais_origen VARCHAR(50) NOT NULL,
    genero_rock VARCHAR(50) NOT NULL,
    esta_activa CHAR(1) DEFAULT 'S',
    CHECK (ano_formacion >= 1950 AND ano_formacion <= 2025),
    CHECK (esta_activa IN ('S', 'N'))
);

-- Tabla de Artistas
CREATE TABLE artistas (
    id_artista INT PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    fecha_nacimiento DATE NOT NULL,
    instrumento_principal VARCHAR(50) NOT NULL,
    pais_nacimiento VARCHAR(50) NOT NULL,
    CHECK (fecha_nacimiento >= '1930-01-01'),
    CHECK (instrumento_principal IN ('Voz', 'Guitarra', 'Bajo', 'Batería', 'Teclado', 'Otro'))
);

-- Tabla Histórico de pertenencia de artistas a bandas
CREATE TABLE artistas_bandas (
    id_artista INT NOT NULL,
    id_banda INT NOT NULL,
    fecha_inicio DATE NOT NULL,
    fecha_fin DATE,
    es_lider CHAR(1) DEFAULT 'N',
    rol_en_banda VARCHAR(100),
    PRIMARY KEY (id_artista, id_banda, fecha_inicio),
    FOREIGN KEY (id_artista) REFERENCES artistas(id_artista),
    FOREIGN KEY (id_banda) REFERENCES bandas(id_banda),
    CHECK (es_lider IN ('S', 'N')),
    CHECK (fecha_fin IS NULL OR fecha_fin > fecha_inicio)
);
```

- **Esquema formal:** $\text{BANDAS}(\underline{\text{id\_banda}}, \text{nombre\_banda}, \text{ano\_formacion}, \text{pais\_origen}, \text{genero\_rock}, \text{esta\_activa})$
    
- **Grado:** 6 (contiene 6 atributos).
    

 Atributos y Dominios

- **`id_banda`:** Dominio de enteros (`INT`). Obligatorio (`NOT NULL`).
    
- **`nombre_banda`:** Dominio de cadenas de caracteres de longitud variable hasta 100 (`VARCHAR(100)`). Obligatorio (`NOT NULL`).
    
- **`ano_formacion`:** Dominio de enteros (`INT`). Obligatorio (`NOT NULL`), acotado al subrango $[1950, 2025]$.
    
- **`pais_origen`:** Dominio de cadenas de caracteres de longitud variable hasta 50 (`VARCHAR(50)`). Obligatorio (`NOT NULL`).
    
- **`genero_rock`:** Dominio de cadenas de caracteres de longitud variable hasta 50 (`VARCHAR(50)`). Obligatorio (`NOT NULL`).
    
- **`esta_activa`:** Dominio de carácter único (`CHAR(1)`). Dominio enumerado `{'S', 'N'}` con valor por defecto `'S'`.

Clasificación de Claves y Atributos
- **Clave Primaria (PK):** $\{\text{id\_banda}\}$.
- **Atributos Primos:** `id_banda`.
- **Atributos No Primos:** `nombre_banda`, `ano_formacion`, `pais_origen`, `genero_rock`, `esta_activa`.
- **Claves Externas (FK):** Ninguna.

 Restricciones de Integridad
- **Integridad de Entidad:** `id_banda` no admite valores nulos ($t[\text{id\_banda}] \neq \text{null}$).
- **Restricciones Semánticas:** `CHECK (ano_formacion >= 1950 AND ano_formacion <= 2025)` y `CHECK (esta_activa IN ('S', 'N'))`.

## Ejercicio 5 - Repaso conceptual

Responde a las siguientes preguntas:

1. ¿Por qué las tuplas de una relación no están ordenadas? Es irrelevante porque una relacion se define matematicamente como un conjunto de tuplas
2. ¿Por qué no se permiten tuplas duplicadas en una relación? Porque violan la restriccion de dominio, sebe ser atomico
3. ¿Cuál es la diferencia entre una clave y una superclave? superclave, es cualquier conjunto de atributos dentro de una relacion que cumple la propiedad de unicidad: es decir que no existen dos tuplas en ningun estado valido con los mismo valores para dicho conjunto de atributos. Cualquier conjunto de atributos que lleve una clave primaria es superclave. Un clave es una superclave minima, es el conjunto de atributos que si le quita  uno deja de ser superclave
4. ¿Por qué designamos una de las claves candidatas de una relación como clave principal? Se elige arbitrariamente una clave candidata como **clave primaria (PK)** para establecer el identificador unívoco y estándar de referencia para cada tupla de la relación. Esta designación es imprescindible para cumplir con la integridad de entidad y para poder vincular registros desde otras tablas mediante claves externas.
5. Analiza las características de las relaciones que las diferencian de las tablas y archivos comunes.
6. Analiza las diversas razones que llevan a la aparición de valores NULL en las relaciones.
7. Analiza las restricciones de integridad de entidad e integridad referencial. ¿Por qué se considera importante cada una?
	1. Integridad de Entidad:
		1. Definición: Establece que ningún atributo que forme parte de la clave primaria de una relación puede tomar valores nulos ($t[PK] \neq \text{null}$).   
		2. Importancia: Es fundamental porque la clave primaria se utiliza para identificar de forma individual y unívoca cada registro. Si se permitiesen nulos, se perdería la capacidad de distinguir entidades en el mundo real. 
	2. Integridad Referencial:
		1. Definición: Garantiza que los valores de una clave externa (FK) en una relación referenciante deben existir en la clave primaria (PK) de la relación referenciada, o bien ser nulos.   
		2. Importancia: Evita la presencia de "tuplas huérfanas" o referencias rotas en la base de datos, asegurando la consistencia lógica en las relaciones entre entidades.   
8. Defina clave externa o foránea. ¿Para qué se utiliza este concepto?
9. ¿Qué es una transacción? ¿En qué se diferencia de una operación de actualización?
	1. Operación de Actualización: Es un comando individual de modificación de datos (INSERT, UPDATE o DELETE) ejecutado sobre una relación.  
	2. Transacción: Es una secuencia completa de operaciones de base de datos que se ejecutan como una única unidad lógica de trabajo indivisible.  
	3. Diferencia Principal: Una operación de actualización actúa sobre datos específicos de forma puntual, mientras que una transacción actúa como un contenedor de una o múltiples actualizaciones que garantiza las propiedades ACID (Atomicidad, Consistencia, Aislamiento y Durabilidad). Es decir, en una transacción, o se ejecutan todas las operaciones con éxito o se revierten por completo ante cualquier fallo. 

## Ejercicio 6 - Restricciones de integridad

Supongamos que cada una de las siguientes operaciones de actualización se aplica directamente al estado de la [[BD Empresa - Estado|base de datos de Empresa]]. Analiza todas las restricciones de integridad violadas por cada operación, si las hay, y las diferentes maneras de aplicar estas restricciones.

1. Insertar (‘Roberto’, ‘Fernández’, ‘Soto’, ‘943775543’, ‘1992-06-21’, ‘AV. Universidad, Madrid, ES’, 'M', 58000, ‘888665555’, 1) en EMPLEADO. No viola ninguna restriccion
2. Insertar (‘ProductoA’, 4, ‘Sevilla’, 2) en PROYECTO. Violada la restriccion referencial añade un dpto que no se encuentra en la tabla dpto
3. Insertar (‘Producción’, 4, ‘943775543’, ‘2007-10-01’) en DEPARTAMENTO. **Clave Primaria (Unicidad)** e **Integridad Referencial**
4. Insertar (‘677678989’, NULL, ‘40.0’) en TRABAJA_EN. Restricciones violadas: Integridad de Entidad e Integridad Referencial.
	1. Integridad de entidad: La clave primaria de TRABAJA_EN es compuesta ({empleado, proyecto}). Ninguno de sus componentes puede ser NULL, por lo que asignar NULL a proyecto está prohibido.  
	2. Integridad referencial: El empleado '677678989' no existe en la tabla EMPLEADO.  
5. Insertar (‘453453453’, ‘Juan’, ‘M’, ‘1990-12-12’, ‘Conyuge’) en FAMILIAR.No viola ninguna restriccion
6. Eliminar las tuplas de TRABAJA_EN con empleado = ‘333445555’. No viola ninguna restriccion
7. Eliminar la tupla de EMPLEADO con dni = ‘987654321’.Restricciones violadas: Integridad Referencial.Análisis de integridad: El DNI '987654321' está referenciado en múltiples tablas: es director en DEPARTAMENTO, es supervisor de otros empleados en EMPLEADO, y posee registros en TRABAJA_EN y FAMILIAR.   Tratamiento del SGBD:ON DELETE RESTRICT / NO ACTION (Comportamiento por defecto): El SGBD rechaza el borrado para evitar referencias rotas.   ON DELETE CASCADE: Si estuviera configurado en las claves ajenas de TRABAJA_EN y FAMILIAR, eliminaría automáticamente las tuplas asociadas a ese empleado en dichas tablas.   ON DELETE SET NULL / Modificación previa: Reasignar a otro director en DEPARTAMENTO y actualizar la columna supervisor en los empleados a su cargo antes de borrar el registro.
8. Eliminar la tupla de PROYECTO con nombre = ‘ProductX’. No viola ninguna restriccion
9. Modificar director y fechaIngresoDirector de la tupla de DEPARTAMENTO con numero = 5 a ‘123456789’ y ‘2007-10-01’, respectivamente. No viola ninguna reestriccion
10. Modificar el atributo supervisor de la tupla de EMPLEADO con dni = '999887777' a '943775543'. Restricciones violadas: Integridad Referencial.

Análisis de integridad: El nuevo valor para la clave externa supervisor ('943775543') no existe como clave primaria (dni) en la tabla referenciada EMPLEADO
1. Modificar el atributo horas de la tupla de TRABAJA_EN con empleado = '999887777' y proyecto = 10 a '5.0'. No viola ninguna restriccion


## Bibliografía

Estos ejercicios han sido extraídos de las siguientes referencias bibliográficas:

1. Garcia-Molina, H., Ullman, J. D., & Widom, J. (2008). Database Systems: The Complete Book.
2. Elmasri, Ramez, & Navathe, Shamkant B. (2017). Fundamentals of Database Systems (7th ed.). Pearson Education Ltd



