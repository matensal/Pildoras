# ABAP for RAP: Entity Manipulation Language (ABAP EML)
- [Definición EML](#definicion).
- [Expresiones contructoras](#constructoras)
  - [Introducción](#introduction)
  - [`VALUE`](#value)
  - [`CORRESPONDING`](#corresponding)
  - [`CONV`](#conv)
  - [`COND`](#cond)
  - [`SWITCH`](#switch)
  - [Expresiones de iteración `FOR`](#iteration-expressions-with-for)
  - [`LET Expressions`](#let-expressions)

## Definición EML
Es un subconjunto de ABAP que permite el acceso a los los datos en los Business Object de RAP, en adelante (BO).

## Expresiones contructoras.
### Introducción
- Son expresiones que nos premiten definir de variables, estructuras o tablas de una forma sencilla y rápida en pocas líneas de código.
  
- Permite la definición inline de las variables en ABAP.

### VALUE 
 - Crea el contenido de una variable de un tipo definido, para ello utilizamos **VALUE #**
    <pre>TYPES: BEGIN OF struc_type,
          a   TYPE i,
          b   TYPE c LENGTH 3,
        END OF struc_type.

    DATA struc TYPE struc_type. "Estructura creada

    struc = VALUE #( a = 1 b = 'aaa' ). "Rellenamos la estructura con los valores a = 1 y b = aaa.
    </pre>
- Crea la definición de la variable y el contenido utilizando **VALUE tipo**
    <pre>
    DATA(structure) = VALUE struc_type( a = 2 b = 'bbb' ).
    </pre>
- Crear tabla, tener en cuenta que tenemos ***dos paréntesis uno para el VALUE y otro para la estructura ( ( ) )***
    <pre>
    DATA itab TYPE tab_type.

    "Tabla interna
    itab = VALUE #( ( a = 1 b = 'aaa' )
                ( a = 2 b = 'bbb' )
                ( a = 3 b = 'ccc' ) ).

    "Tabla interna con declaración inline
    DATA(itab2) = VALUE tab_type( ( a = 1 b = 'aaa' )
                              ( a = 2 b = 'bbb' )
                              ( a = 3 b = 'ccc' ) ).
    </pre>

- Tabla a partir de lineas de otras tablas, añadiremos **LINES OF**.
    <pre>
    itab = VALUE #( ( a = 1 b = 'aaa' )
                ( a = 2 b = 'bbb' )
                ( LINES OF itab2 )    "Todas las líneas de itab2
                ( LINES OF itab3 FROM 2 TO 5 ) ).  "Las lineas 2 a 5 de itab3
    </pre>

## CORRESPONDING
- Asigna los valores de una estrucutura en otra cuyos campos son iguales, aunque tenemos excepciones utilizando **MAPPING o EXCEPT**

  <pre>
    tab2 = CORRESPONDING #( tab1 ).
    

    "Mapea el struc1-comp1 en struc-comp2
    struc2 = CORRESPONDING #( struc1 MAPPING comp1 = comp2 ).

    "Mapea los campos correspondientes a excepción de tab1-comp1
    tab2 = CORRESPONDING #( tab1 EXCEPT comp1 ).
  </pre>

## CONV
- Covierte el dato en el tipo indicado, **CONV tipo( dato )**
  <pre>
  "Resultado: 0.2
  DATA(a) = CONV decfloat34( 1 / 5 ).
  </pre>

## COND  
- Genera el resultado de una condición utilizando **WHEN**, **THEN** o **ELSE**.
  <pre>
  DATA(b) = COND #( WHEN a BETWEEN 1 AND 3 THEN w
                  WHEN a > 4 THEN x
                  WHEN a IS INITIAL THEN y
                  ELSE z ).
  </pre>

## SWITCH
- Genera el resultado de una condición como un **CASE**
  <pre>
    DATA(b) = SWITCH #( a
                    WHEN 1 THEN w
                    WHEN 2 THEN x
                    WHEN 3 THEN y
                    ELSE z ).
  </pre>

## Expresiones de iteración FOR
- Se usa para recorrer tablas pudiendo usar condiciones para recuperar únicamente los registros que cumplen la condición.

### FOR ... WHILE
- **INIT...**: Se especifica una variable temporal que establece un valor inicial para la variable de resultado.
- **FOR...**: Representa un bucle. El bucle se lleva a cabo hasta que se cumple la condición después de **WHILE**.
- **NEXT...**: Representa la asignación a la variable temporal después de cada iteración.
- Una vez finalizado el bucle, se asigna el valor resultante a la variable objetivo.

  <pre>
    DATA(sum) = VALUE i( INIT y = 0
                      FOR n = 1 THEN n + 1 WHILE n < 11
                      NEXT y += n ).
  </pre>

### FOR ... UNTIL
- **INIT...**: Se especifica una variable temporal que establece un valor inicial para la variable de resultado.
- **FOR...**: Representa un bucle. El bucle se lleva a cabo hasta que se cumple la condición después de **UNTIL**.
- **NEXT...**: Representa la asignación a la variable temporal después de cada iteración.
- Una vez finalizado el bucle, se asigna el valor resultante a la variable objetivo.
  <pre>
    DATA(sum) = VALUE i( INIT y = 0
                      FOR n = 1 UNTIL n > 11
                      NEXT y += n ).
  </pre>

### FOR ... IN
- Representa una iteración variable dependiento de la tabla que iteramos. Podemos tener cualquier tipo de combinación. 
  <pre>
  TYPES t_type LIKE itab
  DATA(itab_temp) = VALUE t_type( FOR wa IN itab
                                  WHERE ( comp1 > 2 )
                                  ( wa ) ).
  </pre>
   <pre>
  TYPES t_type LIKE itab
  DATA(itab_temp) = VALUE t_type( FOR wa IN itab2
                                  WHERE ( comp1 > 2 )
                                  ( CORRESPONDING #( wa ) ) ).
  </pre>
   <pre>
  TYPES t_type LIKE itab
  DATA(itab_temp) = VALUE t_type( FOR wa1 IN itab1 WHERE ( comp1 = 4 )
                    FOR wa2 IN itab2 WHERE ( comp2 > 5 )
                    FOR wa3 IN itab3 WHERE ( comp3 < 3 )
                    ( compX = wa1-comp1
                      compY = wa2-comp2
                      compZ = wa3-comp3 ) ).
  </pre>