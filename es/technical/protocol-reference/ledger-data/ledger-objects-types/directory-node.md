# Directory Node

[\[Fuente\]](https://github.com/ripple/rippled/blob/5d2d88209f1732a0f8d592012094e345cbe3e675/src/ripple/protocol/impl/LedgerFormats.cpp#L44)

El tipo de objeto `DirectoryNode` proporciona una lista de enlaces a otros objetos en el árbol de estado del ledger. Un único _Directorio_ conceptual toma la forma de una lista doblemente enlazada, con uno o más objetos DirectoryNode, cada uno de los cuales contiene hasta 32 IDs de otros objetos. El primer objeto se llama la raíz del directorio, y todos los objetos que no sean el objeto raíz pueden ser añadidos o eliminados según sea necesario.

Hay dos tipos de Directorios:

* **Directorios de Propietario** que enumeran otros objetos propiedad de una cuenta, como objetos `RippleState` (línea de confianza) u `Offer`.
* **Directorios de Ofertas** que enumeran las ofertas disponibles en el exchange descentralizado. Un único directorio de Ofertas contiene todas las ofertas que tienen la misma tasa de cambio para el mismo token (código de moneda y emisor).

### Ejemplo de JSON

_Directorio de Ofertas_

```json
{
    "ExchangeRate": "4F069BA8FF484000",
    "Flags": 0,
    "Indexes": [
        "AD7EAE148287EF12D213A251015F86E6D4BD34B3C4A0A1ED9A17198373F908AD"
    ],
    "LedgerEntryType": "DirectoryNode",
    "RootIndex": "1BBEF97EDE88D40CEE2ADE6FEF121166AFE80D99EBADB01A4F069BA8FF484000",
    "TakerGetsCurrency": "0000000000000000000000000000000000000000",
    "TakerGetsIssuer": "0000000000000000000000000000000000000000",
    "TakerPaysCurrency": "0000000000000000000000004A50590000000000",
    "TakerPaysIssuer": "5BBC0F22F61D9224A110650CFE21CC0C4BE13098",
    "index": "1BBEF97EDE88D40CEE2ADE6FEF121166AFE80D99EBADB01A4F069BA8FF484000"
}
```

_Directorio de Propietario_

```json
{
    "Flags": 0,
    "Indexes": [
        "AD7EAE148287EF12D213A251015F86E6D4BD34B3C4A0A1ED9A17198373F908AD",
        "E83BBB58949A8303DF07172B16FB8EFBA66B9191F3836EC27A4568ED5997BAC5"
    ],
    "ReferenceCount": "1",
    "LedgerEntryType": "DirectoryNode",
    "Owner": "rpR95n1iFkTqpoy1e878f4Z1pVHVtWKMNQ",
    "RootIndex": "193C591BF62482468422313F9D3274B5927CA80B4DD3707E42015DD609E39C94",
    "index": "193C591BF62482468422313F9D3274B5927CA80B4DD3707E42015DD609E39C94"
}
```

### Campos

| Nombre                | Tipo JSON | \[Tipo interno]\[] | ¿Necesario? | Descripción                                                                                                                                                                                                                |
| ------------------- | --------- | ------------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Owner`             | String    | AccountID           | No        | (Solo en Directorios de Propietario) La dirección de la cuenta que posee los objetos en este directorio.                                                                                                                               |
| `TakerGetsCurrency` | String    | Hash160             | No        | (Solo en Directorios de Ofertas) El código de divisa de la cantidad `TakerGets` de las ofertas en este directorio.                                                                                                                    |
| `TakerGetsIssuer`   | String    | Hash160             | No        | (Solo en Directorios de Ofertas) El emisor de la cantidad `TakerGets` de las ofertas en este directorio.                                                                                                                           |
| `TakerPaysCurrency` | String    | Hash160             | No        | (Solo en Directorios de Ofertas) El código de divisa de la cantidad `TakerPays` de las ofertas en este directorio.                                                                                                                    |
| `TakerPaysIssuer`   | String    | Hash160             | No        | (Solo en Directorios de Ofertas) El emisor de la cantidad `TakerPays` de las ofertas en este directorio.                                                                                                                           |
| `ExchangeRate`      | String    | UInt64              | No        | (Solo en Directorios de Ofertas) **OBSOLETO**. No usar.                                                                                                                                                                       |
| `ReferenceCount`    | String    | UInt64              | No        | El recuento de referencias en el ledger reduce la carga de almacenamiento y las tarifas al permitir que múltiples instalaciones del mismo Hook apunten al mismo objeto en el ledger, eliminándolo solo cuando ninguna cuenta tenga el Hook instalado. |
| `Indexes`           | Array     | Vector256           | Sí       | El contenido de este Directorio: un array de IDs de otros objetos.                                                                                                                                                          |
| `IndexNext`         | Number    | UInt64              | No        | Si este Directorio consta de varias páginas, este ID enlaza al siguiente objeto en la cadena, envolviéndose al final.                                                                                                   |
| `IndexPrevious`     | Number    | UInt64              | No        | Si este Directorio consta de varias páginas, este ID enlaza al objeto anterior en la cadena, envolviéndose al principio.                                                                                         |
| `NFTokenID`         | String    | Hash256             | No        | No usado.                                                                                                                                                                                                                     |

### Formatos ID de Directorios

Existen tres fórmulas diferentes para crear el ID de un DirectoryNode, dependiendo de lo que represente:

* La primera página (también llamada the root o la raíz) de un Directorio de Propietario.
* La primera página de un Directorio de Ofertas.
* Páginas posteriores de cualquiera de los dos tipos.

**La primera página de un Owner Directory (Directorio de Propietario)** tiene un ID que es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio del Owner Directory (`0x004F`)
* El AccountID del campo `Owner`.

**La primera página de un Offer Directory (o Directorio de Ofertas)** tiene un ID especial: los 192 bits superiores definen el libro de órdenes, y los 64 bits restantes definen la tasa de cambio de las ofertas en ese directorio. (El ID es big-endian, por lo que el libro está en los bits más significativos, que vienen primero, y la calidad está en los bits menos significativos, que vienen al final)&#x20;

Esto proporciona una forma de iterar a través de un libro de órdenes de las mejores ofertas a las peores. Específicamente: los primeros 192 bits son los primeros 192 bits del \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio del Book Directory (`0x0042`)
* El código de moneda de 160 bits de `TakerPaysCurrency`
* El código de moneda de 160 bits de `TakerGetsCurrency`
* El AccountID de `TakerPaysIssuer`
* El AccountID de `TakerGetsIssuer`

Los 64 bits inferiores del ID de un Directorio de Ofertas representan la cantidad de `TakerPays` dividida por la cantidad de `TakerGets` de la(s) oferta(s) en ese directorio como un número de 64 bits en el formato de cantidad interno de Xahau.

**Si el DirectoryNode no es la primera página en el Directory** (independientemente de si es un Owner Directory o un Offer Directory), entonces tiene un ID que es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio del DirectoryNode (`0x0064`)
* El ID del root DirectoryNode (raíz).
* El número de página de este objeto. (Dado que 0 es el root DirectoryNode (raíz), este valor es un número entero 1 o mayor).
