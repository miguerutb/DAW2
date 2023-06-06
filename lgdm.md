# Tema 10 - XPath

## Selección de nodos

| Xpath            | Resultado                                   |
| ---------------- | ------------------------------------------- |
| libreria         | todos los nodos libreria                    |
| /libreria        | nodo raiz libreria                          |
| /libreria/libro  | todos los libros hijos de libreria          |
| libreria/libro   | todos los libros hijos de libreria          |
| //libro          | todos los libros                            |
| libreria//titulo | todos los titulos descendientes de libreria |
| //titulo/..      | todos los padres de titulo                  |
| //@idioma        | todos los atributos idioma                  |

## Comodines

| XPath               | Resultado                                                                    |
| ------------------- | ---------------------------------------------------------------------------- |
| /libreria/\*        | todos los nodos hijo de libreria                                             |
| /libreria/libro/\*  | todos los elementos hijo de todos los elementos libro de libreria            |
| /libreria/\*/titulo | todos los elementos titulo, nietos de libreria, independientemente del padre |
| //\*                | todos los elementos                                                          |
| //titulo[@*]        | todos los elementos titulo con algun atributo                                |

## Predicados

Encontrar un nodo especifíco, o con un valor específico.

| XPath                                | Resultado                                                       |
| ------------------------------------ | --------------------------------------------------------------- |
| /libreria/libro[1]                   | primer elemento libro                                           |
| /libreria/libro[last()-1]            | penúltimo elemento libro                                        |
| /libreria/libro[position()<3]        | primeros dos elementos libro                                    |
| /libreria/libro[precio]              | todos los libros con precio                                     |
| /libreria/libro[precio=35]           | todos los libros con precio=35                                  |
| /libreria/libro[precio>35.00]/titulo | todos los titulos que son hijos de libro y tienen un precio=35  |
| //libro[autor="J.K. Rowling"]        | todos los libros cuyo autor es “J.K. Rowling”                   |
| //titulo[@idioma='inglés']           | todos los elementos titulo que tienen un atributo idioma=inglés |

## Funciones

| XPath                          | Resultado                                          |
| ------------------------------ | -------------------------------------------------- |
| text()                         | contenido de un elemento                           |
| data()                         | contenido de un atributo                           |
| min(_nodo_), max(_nodo_)       | valor minimo y maximo del conjunto de nodos        |
| ceiling(_nodo_), floor(_nodo_) | redondea a la baja y alta respectivamente          |
| contains(_c1_,_c2_)            | **true** si _c1_ contiene _c2_.                    |
| concat(_c1_,_c2_)              | concatenación de la cadena _c1_ con la cadena _c2_ |
| count(_nodos_)                 | número de nodos                                    |
| id(_valor_)                    | Selecciona nodos por su ID único                   |

# Tema 11 - XQuery

Consultas **FLWOR**:

-   For: Recupera nodos mediante XPath y los almacena en una variable
-   Where
-   Order by
-   Return: Devuelve nodos mediante XPath

```xquery
for $libro in /biblioteca/libro
return <tituloLibro>{$libro/titulo/text()}</tituloLibro>
```

## Cláusula _at_

Obtener una variable con la numeracion de la secuencia de nodos recuperados:

```xquery
<biblioteca2>
{
for $libro in /biblioteca/libro
return <tituloLibro>{$libro/titulo/text()}</tituloLibro>
}
</biblioteca2>
```

## Cláusula _let_

Declarar variables y sólo la asigna una vez. Permite funciones de agrupación:

```xquery
let $book := /biblioteca/libro
return <ultimoAnyo>{max($book/anyo)}</ultimoAnyo>
```

## Cláusula _where_

Filtra los nodos de la cláusula _for_

```xquery
for $lib in /biblioteca/libro
where matches($lib/titulo,"ejemplos")
return $lib/titulo
```

## Cláusula _order by_

Ordena la secuencia de nodos **antes** del _return_

```xquery
for $lib in distinct-values(/biblioteca/libro)
order by $lib/titulo
return $lib/titulo
```

# Tema 12 - XSLT

```xml
<?xml-stylesheet href="EjemploXSLT_2.xsl" type="text/xsl"?>
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<xsl:stylesheet xmlns:xsl="http://www.w3.org/1999/XSL/Transform"
	xmlns:xs="http://www.w3.org/2001/XMLSchema"
	xmlns:math="http://www.w3.org/2005/xpath-functions/math"
	xmlns:map="http://www.w3.org/2005/xpath-functions/map"
	xmlns:array="http://www.w3.org/2005/xpath-functions/array"
	exclude-result-prefixes="#all"
	version="3.0">

  <xsl:mode on-no-match="shallow-copy"/>

  <xsl:output method="html" indent="yes" html-version="5"/>

  <xsl:template match="/">
    <html>
      <head>
        <title>.NET XSLT Fiddle Example</title>
        <style>
          .tabla{display:table; margin: auto;}
          .fila{display: table-row;}
          .celda{display: table-cell; padding: 5px; border: 1px solid green;}
        </style>
      </head>
      <body>
        <h1 style ="text-align:center;">Novedades XML</h1>

        <xsl:for-each select="//libreria">
          <div style="background:orange; text-align:center">
            <h2>
              Librería <xsl:value-of select="@nombre"></xsl:value-of>
            </h2>
            <h3>
              <xsl:value-of select="@web"></xsl:value-of>
            </h3>
          </div>

          <div class="tabla">
            <div class="fila">
              <div class="celda">Titulo</div>
              <div class="celda">Autor</div>
              <div class="celda">Editorial</div>
              <div class="celda">Año</div>
              <div class="celda">Nivel</div>
              <div class="celda">ISBN</div>
              <div class="celda">Precio</div>
            </div>

            <xsl:for-each select="libro">
              <xsl:sort select="titulo"/>
                <div class="fila">
                  <xsl:apply-templates select="titulo"/>
                  <xsl:apply-templates select="autores"/>
                  <xsl:apply-templates select="editorial"/>
                  <xsl:apply-templates select="anyo"/>
                  <xsl:apply-templates select="@nivel"/>
                  <xsl:apply-templates select="@isbn"/>
                  <xsl:apply-templates select="precio"/>
                </div>
            </xsl:for-each>
          </div>
        </xsl:for-each>
      </body>
    </html>

  </xsl:template>

  <xsl:template match="precio">
    <div class="celda">
      <xsl:value-of select="."/>
      <xsl:choose>
        <xsl:when test="@moneda = 'Euro'"> €</xsl:when>
        <xsl:when test="@moneda = 'Dolar'"> $</xsl:when>
        <xsl:when test="@moneda = 'Libra'"> £</xsl:when>
      </xsl:choose>
    </div>
  </xsl:template>

  <xsl:template match="@isbn">
    <div class="celda">
      <xsl:value-of select="."/>
    </div>
  </xsl:template>

  <xsl:template match="@nivel">
    <div class="celda">
      <xsl:value-of select="."/>
    </div>
  </xsl:template>

  <xsl:template match="anyo">
    <div class="celda">
      <xsl:value-of select="."/>
    </div>
  </xsl:template>

  <xsl:template match="editorial">
    <div class="celda">
      <xsl:value-of select="."/>
    </div>
  </xsl:template>

  <xsl:template match="text()">
      <xsl:value-of select='.' />
  </xsl:template>

  <xsl:template match="autores">
    <div class="celda">
      <xsl:for-each select="autor">
        <xsl:value-of select="."/>
        <xsl:if test="position() != last()">, </xsl:if>
      </xsl:for-each>
    </div>
  </xsl:template>

  <xsl:template match="titulo">
    <div class="celda">
      <xsl:value-of select="."/>
    </div>
  </xsl:template>
</xsl:stylesheet>
```

https://xsltfiddle.liberty-development.net/bFD9utv/9
