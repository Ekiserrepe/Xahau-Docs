# Hashes de Ledgers

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/impl/LedgerFormats.cpp#L104-L108)

(No se debe confundir con el \ [tipo de dato de cadena "ledger hash"]\[Hash], que identifica de manera única una versión de un ledger. Esta sección describe el tipo de objeto `LedgerHashes` del ledger.)

El tipo de objeto `LedgerHashes` contiene un historial de ledgers anteriores que llevaron a esta versión del ledger, en la forma de sus hashes. Los objetos de este tipo de ledger se modifican automáticamente al cerrar un ledger. (Este es uno de los únicos momentos en que se modifica la información de estado de un ledger sin una transacción o pseudo-transacción). Los objetos `LedgerHashes` existen para que sea posible buscar el hash de un ledger anterior con solo la versión actual del ledger y, como máximo, una búsqueda de una versión anterior del ledger.

Hay dos tipos de objetos `LedgerHashes`. Ambos tipos tienen los mismos campos. Cada versión de ledger contiene:

* Exactamente un objeto `LedgerHashes` de "historial reciente".
* Un número de objetos `LedgerHashes` de "historial anterior" basado en el índice actual del ledger (es decir, la longitud del historial del ledger). Específicamente, Xahau agrega un nuevo objeto de "historial anterior" cada 65536 versiones de ledger.

**Nota:** Como excepción, un nuevo ledger génesis no tiene objetos `LedgerHashes`, porque no tiene historial de ledger.

Ejemplo de objeto `LedgerHashes` (recortado para longitud):

```json
{
  "LedgerEntryType": "LedgerHashes",
  "Flags": 0,
  "FirstLedgerSequence": 2,
  "LastLedgerSequence": 33872029,
  "Hashes": [
    "D638208ADBD04CBB10DE7B645D3AB4BA31489379411A3A347151702B6401AA78",
    "254D690864E418DDD9BCAC93F41B1F53B1AE693FC5FE667CE40205C322D1BE3B",
    "A2B31D28905E2DEF926362822BC412B12ABF6942B73B72A32D46ED2ABB7ACCFA",
    "AB4014846DF818A4B43D6B1686D0DE0644FE711577C5AB6F0B2A21CCEE280140",
    "3383784E82A8BA45F4DD5EF4EE90A1B2D3B4571317DBAC37B859836ADDE644C1",
    ... (up to 256 ledger hashes) ...
  ],
  "index": "B4979A36CDC7F3D3D5C31A4EAE2AC7D7209DDA877588B9AFC66799692AB0D66B"
}
```

Un objeto `LedgerHashes` tiene los siguientes campos:

| Nombre                  | Tipo JSON        | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                                                                                                                                                                                                                                                                        |
| --------------------- | ---------------- | ------------------- | --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `FirstLedgerSequence` | Number           | UInt32              | Sí       | **DESCONTINUADO** No usar. (El objeto de "hashes recientes" en la producción de Xahau tiene el valor `2` en este campo como resultado de una versión anterior del software `rippled`. Ese valor se conserva al actualizar el objeto de "hashes recientes". Nuevos objetos de "historial anterior" no tienen este campo, ni los objetos de "hashes recientes" en redes paralelas iniciadas con versiones más recientes de `rippled`.) |
| `Flags`               | Number           | UInt32              | Sí       | Un mapa de bits de flags booleanos habilitados para este objeto. Actualmente, el protocolo no define flags para los objetos `LedgerHashes`. El valor es siempre `0`.                                                                                                                                                                                                                                                  |
| `Hashes`              | Array of Strings | Vector256           | Sí       | Un array de hasta 256 hashes de ledger. El contenido depende de qué subtipo de objeto `LedgerHashes` sea este.                                                                                                                                                                                                                                                                                       |
| `LastLedgerSequence`  | Number           | UInt32              | Sí       | El \[Índice del Ledger]\[] de la última entrada en el array `Hashes` de este objeto.                                                                                                                                                                                                                                                                                                                          |
| `LedgerEntryType`     | String           | UInt16              | Sí       | El valor `0x0068`, mapeado a la cadena `LedgerHashes`, indica que este objeto es una lista de hashes de ledger.                                                                                                                                                                                                                                                                                    |

### Historial reciente LedgerHashes

Hay exactamente un objeto `LedgerHashes` del subtipo de "historial reciente" en cada ledger después del ledger génesis. Este objeto contiene los hashes identificadores de las versiones más recientes de 256 ledgers (o menos, si el historial del ledger tiene menos de 256 ledgers en total) en la matriz `Hashes`. Siempre que se cierra un nuevo ledger, parte del proceso de cierre incluye la actualización del objeto de "historial reciente" con el hash de la versión anterior del ledger de la que se deriva esta versión del ledger (también conocido como el ledger padre de esta versión del ledger). Cuando hay más de 256 hashes, se elimina el más antiguo.

Usando el objeto "historial reciente" `LedgerHashes` de un ledger dado, puedes obtener el hash de cualquier índice de ledger dentro de las 256 versiones de ledger antes de la versión dada.

### Historial Anterior LedgerHashes

Las entradas de "historial anterior" `LedgerHashes` contienen colectivamente el hash de cada 256ª versión de ledger (también llamada "ledgers flag") en el historial completo del ledger. Cuando se cierra el hijo de un ledger flag, el hash del ledger flag se agrega al array `Hashes` del objeto "historial anterior" `LedgerHashes` más reciente. Cada 65536 ledgers, `rippled` crea un nuevo objeto `LedgerHashes`, para que cada objeto de "historial anterior" tenga los hashes de 256 ledgers flag.

**Nota:** El objeto `LedgerHashes` de "historial anterior" más antiguo contiene solo 255 entradas porque el ledger génesis tiene un índice de ledger de 1, no 0.

Los objetos `LedgerHashes` de "historial anterior" actúan como una [lista de salto](https://en.wikipedia.org/wiki/Skip\_list) para que puedas obtener el hash de cualquier ledger flag histórico desde su índice. A partir de ahí, puedes usar el objeto de "historial reciente" de ese ledger de bandera para obtener el hash de cualquier otro ledger.

### Formatos de ID de LedgerHashes

Hay dos formatos para los IDs de objetos `LedgerHashes`, dependiendo de si el objeto es un subtipo de "historial reciente" o un subtipo de "historial anterior".

El objeto `LedgerHashes` de **"historial reciente"** tiene un ID que es el \[SHA-512Half]\[] de la clave de espacio `LedgerHashes` (`0x0073`). En otras palabras, el "historial reciente" siempre tiene el ID `B4979A36CDC7F3D3D5C31A4EAE2AC7D7209DDA877588B9AFC66799692AB0D66B`.

Los objetos `LedgerHashes` de **"historial anterior"** tienen un ID que es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio `LedgerHashes` (`0x0073`)
* El \[índice de ledger]\[] de 32 bits de un ledger flag en el array `Hashes` del objeto, dividido por 65536.

    **Consejo:** Dividir por 65536 conserva los 16 bits más significativos, que son los mismos para todos los ledgers flag enumerados en un objeto de "historial anterior", y solo esos ledgers. Puedes usar este hecho para buscar el objeto `LedgerHashes` que contiene el hash de cualquier ledger flag.
