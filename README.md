Guía para la Construcción de APIs (alpha)
=================================

##### Estándares de creación de interfaces de programación de aplicaciones (APIs) en el Gobierno de Chile
##### Unidad de modernización y gobierno digital (www.modernizacion.gob.cl)

## Indice ##

 - [Objetivo del documento](#objetivo-del-documento)
 - [Formato de URLs](#formato-de-urls)
 - [Encabezados HTTP](#encabezados-http)
 - [Tipos de datos](#tipos-de-datos)
 - [Métodos HTTP](#metodos-http)
 - [Respuestas](#respuestas)
 - [Manejo de Errores](#manejo-de-errores)
 - [Versiones](#versiones)
 - [Límites de Registros](#limites-de-registros)
 - [Codificación de caracteres](#codificacion-de-caracteres)
 - [JSONP](#jsonp)
 - [Referencias](#referencias)

## Objetivo del documento ##

Entregar lineamientos técnicos para la construcción de servicios web (REST) para las aplicaciones creadas por el gobierno de Chile.

## Formato de URLs ##

El número de versión de la API debe ir incluida en la base de la URL, asi como también la extensión del formato en el cual serán devueltos los datos.

 - Ej: https://ejemplo.cl/api/v2/solicitudes.json

Una URL debe identificar un recurso o una colección de recursos.

La URL debe contener sustantivos, no verbos.

Debe contener sustantivos en su forma plural.

Se usan los métodos HTTP (GET, POST, PUT, DELETE y PATCH) para operar sobre un recurso o una colección de recursos.

La profundidad de una URL que describe un recurso no deberá ser más profunda que recurso/identificador/recurso.

Atributos opcionales:
Si cambian la lógica del resultado, deben ir en la url. Si son múltiples, deben ir separados por coma.

 - Ej: https://ejemplo.cl/api/v2/solicitudes.json?tema=salud,educacion&sort=asc

Si no cambian la lógica del resultado, deben ir en el header HTTP.

 - Ej: Autenticación OAuth

No utilizar acentos ni caracteres especiales en la URL.

#### Buenos Ejemplos ####

 - Listado de solicitudes
    - GET https://ejemplo.cl/api/v1/solicitudes.json
 - Listado de solicitudes filtrado por tema y ordenado
    - GET https://ejemplo.cl/api/v1/solicitudes.json?tema=salud,educacion&sort=asc

#### Malos Ejemplos ####

 - Sustantivos en singular
    - GET https://ejemplo.cl/api/v1/solicitud
    - GET https://ejemplo.cl/api/v1/solicitud/123
 - Verbos en la URL
    - POST https://ejemplo.cl/api/v1/solicitud/crear
 - Atributos de filtro opcionales dentro de los segmentos de la URL
    - GET https://ejemplo.cl/api/v1/solicitudes/asc

## Encabezados HTTP ##


Dentro de los encabezados HTTP se deberá incluir el encabezado:

 - Content-type: application/json


## Tipos de datos ##

El formato a usar será JSON.

JSON admite distintos tipos de datos estandar:

 - Number: Un numero que puede o no contener decimales.
    - Ej: 54, 55.3, 103.5
 - String: Una cadena de caracteres.
    - Ej: "hola mundo", "auto", "mesa"
 - Boolean: Indica verdadero o falso
    - Ej: true, false
 - Array: Un conjunto de elementos.
    - Ej: ["hola mundo", "mesa", "auto"]
 - Object: Se puede definir como un mapa de elementos.
    - Ej: {"id": 2, "nombre": "Arturo", "apellido": "zamorano"}
 - Null: Valor nulo
    - Ej: null

Algunos tipos de datos no son parte del estandar JSON, pero esta seria su forma recomendada de representarlos:

 - Fechas: Se debera usar un string con la fecha formateada usando el estandar ISO 8601
    - Ej: “2001-08-22”, “18:30:45”, “1997-07-16T19:20:30”

## Métodos HTTP ##

Los métodos HTTP a usar deben ir en concordancia con lo definido en el estándar HTTP. El método HTTP se aplicará sobre el recurso indicado en la URL.


La siguiente tabla muestra un resumen de los métodos HTTP y su uso:


| Método HTTP | Significado |
| --- | --- |  
| GET | Obtiene un recurso |  
| POST | Crea un recurso |
| PUT | Actualiza un recurso |
| DELETE | Borra un recurso |

Ejemplos:


| Recurso | GET | POST | PUT | DELETE |
| --- | --- | --- | --- | --- |
| /mapaches.json | Obtiene el listado de mapaches | Crea un mapache nuevo | Actualización masiva de mapaches | Borra todos los mapaches |
| /mapaches/123.json | Obtiene el mapache identificado como 123 | Error | Si existe el mapache 123 lo actualiza, si no error | Borra el mapache identificado como 123 |

## Respuestas ##

Las respuestas deberán ir en formato JSON.

Si es una colección de elementos, deberán venir en un arreglo JSON.
Si es un elemento deberá venir en un objeto JSON.

En las llaves no deberán venir valores.

Si la respuesta requiere metadata adicional, esta metadata debera incluir información sobre las propiedades de la respuesta. No sobre los miembros de la respuesta en si.

#### Buenos ejemplos ####

```javascript
{
  "solicitudes": [
    {"id": "125", "nombre": "Solicitud de acceso a ..."},
    {"id": "834", "nombre": "Solicitud de acceso a ..."}
  ]
}
```

#### Malos ejemplos ####

##### Valor identificatorio se esta usando como llave. #####
```javascript
{
"solicitudes": [
    {"125": "Solicitud de acceso a ..."},
    {"834": "Solicitud de acceso a ..."}
  ]
}
```

## Manejo de Errores ##


Los errores deberán ser devueltos utilizando los errores estándares HTTP en el header de respuesta.


| Código HTTP | Descripción |
| --- | --- |
| 200 | Ok |
| 400 | Parametros incorrectos en la entrada |
| 401 | Token de acceso expirado o invalido |
| 403 | Autenticación OAuth incorrecta |
| 404 | Recurso no encontrado |
| 405 | Método HTTP no esperado. Por ej: se esperaba un HTTP GET y se recibió un HTTP POST |
| 429 | Se están recibiendo muchos requests de parte de tu app. Se esta limitando el acceso |
| 500 | Error interno del servidor. |

Además, en el contenido de la respuesta se deberá incluir un objeto JSON con los siguientes campos:

| Llave | Valor |
| --- | --- |
| codigo | Código interno de error |
| mensaje | Descripción del mensaje de error |
| masInfoURL (Opcional) | URL donde se describe más información sobre el mensaje de error |

## Versiones ##

Las APIs siempre deberán incluir un número de versión.

La numeración de las versiones solamente deberá incluir números enteros. No decimales.

Se debe anteponer el carácter ‘v’ antes del numero de version.

Buenos ejemplos

 - v1 v2 v3 v4

Malos ejemplos

 - v1,5 v1.5 1.8

## Límites de Registros ##

Si no se especifica un límite, entregar los resultados con un límite por defecto.

Para especificar límites utilizar parámetros opcionales limit y offset.

| Parámetro | Descripción |
| --- | --- |
| offset | Cuantos registros debe saltarse antes de comenzar |
| limit | Cantidad de registros a desplegar |

Ej: Para obtener los registros del 51 al 78:

 - https://ejemplo.cl/v1/solicitudes.json?offset=50&limit=28


## Codificación de caracteres ##


La codificación a utilizar deberá ser UTF-8.


Los caracteres especiales pueden ser escapados según lo especificado en el RFC 4627.

## JSONP ##


JSONP o JSON con padding es una técnica de comunicación utilizada en los programas JavaScript para realizar llamadas asíncronas a dominios diferentes. JSONP es un método concebido para suplir la limitación de AJAX entre dominios, que únicamente permite realizar peticiones a páginas que se encuentran bajo el mismo dominio y puerto por razones de seguridad.


Utilizando JSONP se puede permitir el uso de nuestra API desde un navegador web que este visitando una web con un dominio distinto al de nuestra API.


Para habilitar JSONP, nos podemos guiar con el siguiente ejemplo.


Si el recurso original es:

 - http://ejemplo.cl/v1/solicitudes.json

Si la respuesta original es:

```javascript
{
  solicitudes: [
    {"id":435, "nombre": "Solicitud ..."}
  ]
}
```


Para solicitar el resultado con JSONP deberemos incluir el parametro callback

 - http://ejemplo.cl/v1/solicitudes.json?callback=micallback

La respuesta que debería ser entregada es
```javascript
micallback({
  solicitudes: [
    {"id":435, "nombre": "Solicitud ..."}
  ]
});
```

## Referencias ##

 - [Whitehouse.gob API Standards](https://github.com/WhiteHouse/api-standards)
 - [Dropbox.com Core API reference](https://www.dropbox.com/developers/core)
 - [Best Practices for a Pragmatic Restful API](http://www.vinaysahni.com/best-practices-for-a-pragmatic-restful-api)
 - [json-p.org](http://json-p.org/)
 - [RFC 4627](https://tools.ietf.org/html/rfc4627)
 - [RFC 7159](https://tools.ietf.org/html/rfc7159)
 - [RFC 5789](https://tools.ietf.org/html/rfc5789)
 - [ISO 8601](http://www.w3.org/TR/NOTE-datetime)

##### Unidad de modernización y gobierno digital - Versión 1.0
