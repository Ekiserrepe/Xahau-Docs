# Formato binario

[\[Fuente\]](https://github.com/XRPLF/rippled/blob/develop/src/ripple/protocol/impl/STObject.cpp#L696-L718)

Esta página describe el formato binario canónico de Xahau para transacciones y otros datos. Este formato binario es necesario para crear y verificar firmas digitales del contenido de esas transacciones, y también se utiliza en otros lugares, como en las comunicaciones entre servidores peer-to-peer. Las APIs de `xahaud` normalmente utilizan JSON para comunicarse con aplicaciones cliente. Sin embargo, JSON no es adecuado como formato para serializar transacciones que serán firmadas digitalmente, porque JSON puede representar los mismos datos de muchas maneras diferentes pero equivalentes.

El proceso de serializar una transacción desde JSON o cualquier otra representación a su formato binario canónico se puede resumir en los siguientes pasos:

1.  Asegúrate de que se proporcionen todos los campos requeridos, incluidos aquellos que son obligatorios pero que se pueden "autocompletar".

    La referencia de formatos de transacciones define los campos requeridos y opcionales para las transacciones de Xahau.

    **Nota:** El `SigningPubKey` también debe proporcionarse en este paso. Al firmar, puedes derivar esta clave de la clave secreta que se proporciona para la firma.
2. Convierte los datos de cada campo a su formato binario "interno".
3. Ordena los campos en orden canónico.
4. Prefija cada campo con un ID de Campo.
5. Concatena los campos (incluidos los prefijos) en su orden clasificado.

El resultado es un único blob binario que se puede firmar utilizando algoritmos de firma bien conocidos, como ECDSA (con la curva elíptica secp256k1) y Ed25519. Para los fines de Xahau, también debes \[hashear]\[Hash] los datos con el prefijo apropiado (`0x53545800` si es una firma única, o `0x534D5400` si es una firma múltiple). Después de firmar, debes volver a serializar la transacción con el campo `TxnSignature` incluido.

**Nota:** Xahau utiliza el mismo formato de serialización para representar otros tipos de datos, como objetos del libro mayor y transacciones procesadas. Sin embargo, solo ciertos campos son apropiados para incluir en una transacción que se firmará. (Por ejemplo, el campo `TxnSignature`, que contiene la firma en sí, no debe estar presente en el blob binario que se firma). Por lo tanto, algunos campos están designados como campos de "Firma", que se incluyen en los objetos cuando esos objetos se firman, y campos "no de firma", que no se incluyen.

#### Ejemplos

Tanto las transacciones firmadas como las no firmadas pueden representarse tanto en formato JSON como binario. Las siguientes muestras muestran la misma transacción firmada en sus formatos JSON y binario:

**JSON:**

```json

<div data-gb-custom-block data-tag="include" data-0='_code-samples/tx-serialization/py/test-cases/tx1.json'></div>

```

**Binario (representado como hexadecimal):**

```

<div data-gb-custom-block data-tag="include" data-0='_code-samples/tx-serialization/py/test-cases/tx1-binary.txt'></div>

```

### Codigo de ejemplo

Los procesos de serialización descritos aquí están implementados en varios lugares y lenguajes de programación:

* En C++ [en el código base de `rippled`](https://github.com/XRPLF/rippled/blob/develop/src/ripple/protocol/impl/STObject.cpp).
* En JavaScript en la sección de ejemplos de código de este repositorio.
* En Python 3 en la sección de ejemplos de código de este repositorio.

Además, muchas bibliotecas cliente proporcionan soporte de serialización bajo licencias de código abierto permisivas, por lo que puedes importar, usar o adaptar el código según tus necesidades.

### Formato interno

Cada campo tiene un formato binario "interno" utilizado en el código fuente de `xahaud` para representar ese campo al firmar (y en la mayoría de los otros casos). Los formatos internos de todos los campos están definidos en el código fuente de [`SField.cpp`](https://github.com/XRPLF/rippled/blob/master/src/ripple/protocol/impl/SField.cpp). (Este archivo también incluye campos que no son campos de transacción). La referencia de formatos de transacciones también enumera los formatos internos de todos los campos de transacciones.

Por ejemplo, el campo común de transacciones `Flags` se convierte en un UInt32 (entero sin signo de 32 bits).

#### Archivo de definiciones

El siguiente archivo JSON define las constantes importantes que necesitas para serializar datos de Xahau a su formato binario y deserializarlos desde el formato binario:

[**https://github.com/XRPLF/xrpl.js/blob/main/packages/ripple-binary-codec/src/enums/definitions.json**](https://github.com/XRPLF/xrpl.js/blob/main/packages/ripple-binary-codec/src/enums/definitions.json)

La siguiente tabla define los campos de nivel superior del archivo de definiciones:

| Campo                 | Contenido                                                                                                                                                                                                                                                                                                                                                  |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `TYPES`               | Mapa de tipos de datos a su "código de tipo" para construir IDs de campo y ordenar campos en orden canónico. Los códigos por debajo de 1 no deben aparecer en datos reales; los códigos superiores a 10000 representan tipos de objetos especiales de "alto nivel", como "Transaction", que no pueden serializarse dentro de otros objetos. Consulta la Lista de Tipos para obtener detalles sobre cómo serializar cada tipo. |
| `LEDGER_ENTRY_TYPES`  | Mapa de objetos del ledger a su tipo de datos. Estos aparecen en los datos de estado del ledger y en la sección de "nodos afectados" de los metadatos de transacciones procesadas.                                                                                                                                                                                                     |
| `FIELDS`              | Una array ordenado de tuplas que representan todos los campos que pueden aparecer en transacciones, objetos del ledger u otros datos. El primer miembro de cada tupla es el nombre de la cadena del campo y el segundo miembro es un objeto con las propiedades de ese campo. (Consulta la tabla "Propiedades del Campo" a continuación para obtener definiciones de esos campos).                                |
| `TRANSACTION_RESULTS` | Mapa de códigos de resultados de transacciones a sus valores numéricos. Los tipos de resultados no incluidos en los libros mayores tienen valores negativos; `tesSUCCESS` tiene un valor numérico de 0; los códigos de la clase `tec` representan fallos que se incluyen en los ledgers.                                                                                                                                  |
| `TRANSACTION_TYPES`   | Mapa de todos los tipos de transacciones a sus valores numéricos.                                                                                                                                                                                                                                                                                                     |

Para fines de serialización de transacciones para firmar y enviar, los campos `FIELDS`, `TYPES` y `TRANSACTION_TYPES` son necesarios.

Los objetos de definición de campo en el array `FIELDS` tienen los siguientes campos:

| Campo            | Tipo    | Contenido                                                                                                                                                                                              |
| ---------------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `nth`            | Number  | El código de campo de este campo, para usar en la construcción de su ID de Campo y ordenarlo con otros campos del mismo tipo de datos.                                                                           |
| `isVLEncoded`    | Boolean | Si `true`, este campo tiene una longitud prefijada.                                                                                                                                                             |
| `isSerialized`   | Boolean | Si `true`, este campo debe codificarse en datos binarios serializados. Cuando este campo es `false`, el campo se reconstruye típicamente a demanda en lugar de almacenarse.                                   |
| `isSigningField` | Boolean | Si `true`, este campo debe serializarse al preparar una transacción para su firma. Si es `false`, este campo debe omitirse de los datos que se firmarán. (Puede que no forme parte de las transacciones en absoluto). |
| `type`           | String  | El tipo de datos internos de este campo. Esto se asigna a una clave en el mapa `TYPES`, que proporciona el código de tipo para este campo.                                                                                |

#### IDs de campo

[\[Fuente - Codificación\]](https://github.com/seelabs/rippled/blob/cecc0ad75849a1d50cc573188ad301ca65519a5b/src/ripple/protocol/impl/Serializer.cpp#L117-L148) [\[Fuente - Decodificación\]](https://github.com/seelabs/rippled/blob/cecc0ad75849a1d50cc573188ad301ca65519a5b/src/ripple/protocol/impl/Serializer.cpp#L484-L509)

Cuando combinas el código de tipo de un campo y el código de campo, obtienes el identificador único del campo, que se antepone antes del campo en el blob serializado final. El tamaño del ID de Campo es de uno a tres bytes dependiendo de los códigos de tipo y de campo que combine. Consulta la siguiente tabla:

|                      | Type Code < 16 | Type Code >= 16 |
| -------------------- | -------------- | --------------- |
| **Field Code < 16**  |                |                 |
| **Field Code >= 16** |                |                 |

Al decodificar, puedes determinar cuántos bytes tiene el ID de campo según qué bits **del primer byte** son ceros. Esto corresponde a los casos de la tabla anterior:

|                            | Los 4 bits altos no son cero                                                                                     | Los 4 bits altos son cero                                                              |
| -------------------------- | ----------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| **Los 4 bits bajos no son cero** | 1 byte: los 4 bits altos definen el tipo; los 4 bits bajos definen el campo.	                                                   | 2 bytes: los 4 bits bajos del primer byte definen el campo; el siguiente byte define el tipo        |
| **Los 4 bits bajos son cero**    | 2 bytes: los 4 bits altos del primer byte definen el tipo; los 4 bits bajos del primer byte son 0; el siguiente byte define el campo | 3 bytes: el primer byte es `0x00`, el segundo byte define el tipo; el tercer byte define el campo |

**Precaución:** Aunque el ID de Campo consta de los dos elementos que se utilizan para ordenar campos, no debes ordenar por el ID de Campo serializado en sí, porque la estructura de bytes del ID de Campo cambia el orden de clasificación.

#### Prefijado de longitud

Algunos tipos de campos de longitud variable están prefijados con un indicador de longitud. Los campos `Blob` (que contienen datos binarios arbitrarios) son uno de estos tipos. Para obtener una lista de qué tipos están prefijados con longitud, consulta la tabla de Lista de Tipos.

**Nota:** Algunos tipos de campos que varían en longitud no están prefijados con longitud. Esos tipos tienen otras formas de indicar el final de sus contenidos.

El prefijo de longitud consiste en uno a tres bytes que indican la longitud del campo inmediatamente después del prefijo de tipo y antes de los contenidos.

* Si el campo contiene de 0 a 192 bytes de datos, el primer byte define la longitud de los contenidos, y luego sigue inmediatamente después del byte de longitud esa cantidad de bytes de datos.
*   Si el campo contiene de 193 a 12480 bytes de datos, los primeros dos bytes indican la longitud del campo con la siguiente fórmula:

    ```
      193 + ((byte1 - 193) * 256) + byte2
    ```
*   Si el campo contiene de 12481 a 918744 bytes de datos, los primeros tres bytes indican la longitud del campo con la siguiente fórmula:

    ```
      12481 + ((byte1 - 241) * 65536) + (byte2 * 256) + byte3
    ```
* Un campo con prefijo de longitud no puede contener más de 918744 bytes de datos.

Al decodificar, puedes determinar a partir del valor del primer byte de longitud si hay 0, 1 o 2 bytes de longitud adicionales:

* Si el primer byte de longitud tiene un valor de 192 o menos, entonces ese es el único byte de longitud y contiene la longitud exacta de los contenidos del campo en bytes.
* Si el primer byte de longitud tiene un valor de 193 a 240, entonces hay dos bytes de longitud.
* Si el primer byte de longitud tiene un valor de 241 a 254, entonces hay tres bytes de longitud.

### Orden Canónico de Campos

Todos los campos en una transacción se ordenan en un orden específico basado primero en el tipo de campo (específicamente, un "código de tipo" numérico asignado a cada tipo), y luego en el campo en sí (un "código de campo"). (Piénsalo como ordenar por apellido, luego por nombre, donde el apellido es el tipo del campo y el nombre es el campo en sí).

#### Códigos de Tipo

Cada tipo de campo tiene un código de tipo arbitrario, con códigos más bajos ordenando primero. Estos códigos están definidos en [`SField.h`](https://github.com/XRPLF/rippled/blob/master/src/ripple/protocol/SField.h#L57-L74).

Por ejemplo, [UInt32 tiene el código de tipo 2](https://github.com/XRPLF/rippled/blob/72e6005f562a8f0818bc94803d222ac9345e1e40/src/ripple/protocol/SField.h#L59), por lo que todos los campos UInt32 vienen antes de todos los campos [Amount, que tienen el código de tipo 6](https://github.com/XRPLF/rippled/blob/72e6005f562a8f0818bc94803d222ac9345e1e40/src/ripple/protocol/SField.h#L63).

El archivo de definiciones enumera los códigos de tipo para cada tipo en el mapa `TYPES`.

#### Códigos de Campo

Cada campo tiene un código de campo, que se usa para ordenar campos que tienen el mismo tipo que otros, con códigos más bajos ordenando primero. Estos campos están definidos en [`SField.cpp`](https://github.com/XRPLF/rippled/blob/72e6005f562a8f0818bc94803d222ac9345e1e40/src/ripple/protocol/impl/SField.cpp#L72-L266).

Por ejemplo, el campo `Account` de una \[transacción de Pago]\[] [tiene el código de orden 1](https://github.com/XRPLF/rippled/blob/72e6005f562a8f0818bc94803d222ac9345e1e40/src/ripple/protocol/impl/SField.cpp#L219), por lo que viene antes del campo `Destination`, que [tiene el código de orden 3](https://github.com/XRPLF/rippled/blob/72e6005f562a8f0818bc94803d222ac9345e1e40/src/ripple/protocol/impl/SField.cpp#L221).

Los códigos de campo se reutilizan para campos de diferentes tipos de campo, pero los campos del mismo tipo nunca tienen el mismo código de campo. Cuando combinas el código de tipo con el código de campo, obtienes el ID de Campo único.

### Lista de Tipos

Las instrucciones de transacción pueden contener campos de cualquiera de los siguientes tipos:

| Nombre del tipo | Código del tipo | Longitud en bits | ¿Prefijado con longitud? | Description                                                                                                                                                                                                  |
| --------- | --------- | ---------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| AccountID | 8         | 160        | Sí              | El identificador único para una cuenta.                                                                                                                                                                        |
| Amount    | 6         | 64 or 384  | No               | Una cantidad de XAH o tokens. La longitud del campo es de 64 bits para XAH o 384 bits (64+160+160) para tokens.                                                                                                  |
| Blob      | 7         | Variable   | Sí              | Datos binarios arbitrarios. Un campo importante de este tipo es `TxnSignature`, la firma que autoriza una transacción.                                                                                              |
| Hash128   | 4         | 128        | No               | Un valor binario arbitrario de 128 bits. El único campo de este tipo es `EmailHash`, que está destinado a almacenar el hash MD-5 del correo electrónico del propietario de una cuenta para obtener un [Gravatar](https://www.gravatar.com/). |
| Hash160   | 17        | 160        | No               | Un valor binario arbitrario de 160 bits. Este puede definir un código de moneda o emisor.                                                                                                                                 |
| Hash256   | 5         | 256        | No               | Un valor binario arbitrario de 256 bits. Esto generalmente representa el "SHA-512Half" hash de una transacción, versión del ledger u objeto de datos del ledger.                                                                    |
| PathSet   | 18        | Variable   | No               | Un conjunto de posibles rutas de pago para un pago entre divisas.                                                                                                                                                |
| STArray   | 15        | Variable   | No               | Un array que contiene un número variable de miembros, que pueden ser de diferentes tipos dependiendo del campo. Dos casos de esto incluyen memos y listas de firmantes utilizados en firmas múltiples.                           |
| STIssue   | 24        | 160 or 320 | No               | Una definición de activo, XAH o un token, sin cantidad.                                                                                                                                                       |
| STObject  | 14        | Variable   | No               | Un objeto que contiene uno o más campos anidados.                                                                                                                                                              |
| UInt8     | 16        | 8          | No               | Un entero sin signo de 8 bits.                                                                                                                                                                                   |
| UInt16    | 1         | 16         | No               | Un entero sin signo de 16 bits. El `TransactionType` es un caso especial de este tipo, con cadenas específicas que se asignan a valores enteros.                                                                            |
| UInt32    | 2         | 32         | No               | Un entero sin signo de 32 bits. Los campos `Flags` y `Sequence` en todas las transacciones son ejemplos de este tipo.                                                                                                  |

Además de todos los tipos de campo anteriores, los siguientes tipos pueden aparecer en otros contextos, como objetos del ledger y metadatos de transacciones:

| Nombre del tipo   | Código del tipo | ¿Longitud prefijada? | Descripción                                                                                                                                                                 |
| ----------- | --------- | ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Transaction | 10001     | No               | Un tipo de "alto nivel" que contiene una transacción completa.                                                                                                                       |
| LedgerEntry | 10002     | No               | Un tipo de "alto nivel" que contiene un objeto completo del ledger.                                                                                                                     |
| Validation  | 10003     | No               | Un tipo de "alto nivel" utilizado en comunicaciones peer-to-peer para representar un voto de validación en el proceso de consenso.                                                            |
| Metadata    | 10004     | No               | Un tipo de "alto nivel" que contiene metadatos para una transacción.                                                                                                                |
| UInt64      | 3         | No               | Un entero sin signo de 64 bits. Este tipo no aparece en las instrucciones de transacción, pero varios objetos del libro mayor utilizan campos de este tipo.                                       |
| Vector256   | 19        | Sí              | Este tipo no aparece en las instrucciones de transacción, pero el campo `Amendments` del objeto del libro mayor de Enmiendas utiliza esto para representar qué enmiendas están habilitadas actualmente. |

#### Campos AccountID

Los campos de este tipo contienen el identificador de 160 bits para una cuenta en el XRP Ledger. En JSON, estos campos se representan como "direcciones" XRP Ledger en formato \[base58]\[], con datos de suma de verificación adicionales para que sea poco probable que los errores tipográficos den como resultado direcciones válidas. (Esta codificación, a veces llamada "Base58Check", evita el envío accidental de dinero a la dirección equivocada). El formato binario para estos campos no contiene datos de suma de verificación ni incluye el "prefijo de tipo" `0x00` utilizado en la codificación base58 de direcciones. (Sin embargo, dado que el formato binario se utiliza principalmente para transacciones firmadas, un error tipográfico u otro error al transcribir una transacción firmada invalidaría la firma, evitando que se envíe dinero).

Las IDs de cuenta que aparecen como campos independientes (como `Account` y `Destination`) están prefijadas con longitud a pesar de tener una longitud fija de 160 bits. Como resultado, el indicador de longitud para estos campos es siempre el byte `0x14`. Las IDs de cuenta que aparecen como subcampos de campos especiales (`issuer` de Amount y `account` de PathSet) _no_ están prefijadas con longitud.

#### Campos Amount

El tipo "Amount" es un tipo de campo especial que representa una cantidad de moneda, ya sea XAH o un token. Este tipo consta de dos subtipos:

*   XAH

    XAH se serializa como un entero sin signo de 64 bits (en orden big-endian), excepto que el bit más significativo es siempre 0 para indicar que es XAH, y el segundo bit más significativo es `1` para indicar que es positivo. Dado que la cantidad máxima de XAH (1017 drops) solo requiere 57 bits, puedes calcular el formato serializado de XAH tomando un entero sin signo estándar de 64 bits y realizando una operación bitwise-OR con `0x4000000000000000`.
*   **Tokens**

    Los tokens constan de tres segmentos en orden:

    1. 64 bits indicating the amount in the token amount format. The first bit is `1` to indicate that this is not XAH.
    2. 160 bits indicating the currency code. The standard API converts 3-character codes such as "USD" into 160-bit codes using the standard currency code format, but custom 160-bit codes are also possible.
    3. 160 bits indicating the issuer's Account ID. (See also: Account Address Encoding)

Puedes saber cuál de los dos subtipos es basado en el primer bit: `0` para XAH; `1` para tokens.

El siguiente diagrama muestra los formatos de serialización para cantidades de XAH y cantidades de tokens:

\{{ include\_svg("img/serialization-amount.svg", 'Las cantidades de XAH tienen un bit de "no XAH", un bit de signo y 62 bits de precisión. Las cantidades de tokens consisten en un bit de "no XRP", un bit de signo, un exponente (8 bits), dígitos significativos (54 bits), código de moneda (160 bits) y emisor (160 bits).') \}}

**Formato de Cantidad de Tokens**

[\[Fuente\]](https://github.com/XRPLF/rippled/blob/35fa20a110e3d43ffc1e9e664fc9017b6f2747ae/src/ripple/protocol/impl/STAmount.cpp)

\{{ include\_svg("img/currency-number-format.svg", "Diagrama del Formato de Cantidad de Tokens") \}}

Xahau utiliza 64 bits para serializar la cantidad numérica de un token fungible. (En formato JSON, la cantidad numérica es el campo `value` de un objeto de cantidad de divisa). En formato binario, la cantidad numérica consta de un bit de "no XAH", un bit de signo, dígitos significativos y un exponente, en orden:

1. El primer bit (más significativo) para una cantidad de token es `1` para indicar que no es una cantidad de XAH. (Las cantidades de XAH siempre tienen el bit más significativo configurado en `0` para distinguirlas de este formato).
2. El bit de signo indica si la cantidad es positiva o negativa. A diferencia de los enteros estándar en [complemento a dos](https://en.wikipedia.org/wiki/Two's\_complement), `1` indica **positivo** en el formato de Xahau, y `0` indica negativo.
3. Los siguientes 8 bits representan el exponente como un entero sin signo. El exponente indica la escala (a qué potencia de 10 deben multiplicarse los dígitos significativos) en el rango de -96 a +80 (inclusive). Sin embargo, al serializar, se suma 97 al exponente para que se pueda serializar como un entero sin signo. Por lo tanto, un valor serializado de `1` indica un exponente de `-96`, un valor serializado de `177` indica un exponente de 80, y así sucesivamente.
4. Los 54 bits restantes representan los dígitos significativos (a veces llamados una mantissa) como un entero sin signo. Al serializar, este valor se normaliza al rango 1015 (`1000000000000000`) a 1016-1 (`9999999999999999`) inclusive, excepto en el caso especial del valor 0. En el caso especial de 0, el bit de signo, el exponente y los dígitos significativos son todos ceros, por lo que el valor de 64 bits se serializa como `0x8000000000000000000000000000000000000000`.


The remaining 54 bits represent the significant digits (sometimes called a _mantissa_) as an unsigned integer. When serializing, this value is normalized to the range 1015 (`1000000000000000`) to 1016-1 (`9999999999999999`) inclusive, except for the special case of the value 0. In the special case for 0, the sign bit, exponent, and significant digits are all zeroes, so the 64-bit value is serialized as `0x8000000000000000000000000000000000000000`.

La cantidad numérica se serializa junto con el código de divisa y el emisor para formar una cantidad de token completa.

**Códigos de divisa**

A nivel de protocolo, los códigos de moneda en Xahau son valores arbitrarios de 160 bits, excepto que los siguientes valores tienen un significado especial:

* El código de divisa `0x0000000000000000000000005852500000000000` está **siempre prohibido**. (Este es el código "XAH" en el "formato estándar").
* El código de moneda `0x0000000000000000000000000000000000000000` (todo ceros) está **generalmente prohibido**. Normalmente, las cantidades de XAH no se especifican con códigos de moneda. Sin embargo, este código se utiliza para indicar XAH en casos raros donde un campo debe especificar un código de moneda para XAH.

Las APIs de `xahaud` admiten un formato estándar para traducir códigos ASCII de tres caracteres a valores hexadecimales de 160 bits de la siguiente manera:

\{{ include\_svg("img/currency-code-format.svg", "Diagrama del Formato de Código de Moneda Estándar") \}}

1. Los primeros 8 bits deben ser `0x00`.
2. Los siguientes 88 bits están reservados y deben ser todos `0`'s.
3. Los siguientes 24 bits representan 3 caracteres de ASCII. Ripple recomienda usar códigos de [ISO 4217](https://www.xe.com/iso4217.php), o pseudo-códigos ISO 4217 populares como "BTC". Sin embargo, se permite cualquier combinación de los siguientes caracteres: todas las letras mayúsculas y minúsculas, dígitos, así como los símbolos `?`, `!`, `@`, `#`, `$`, `%`, `^`, `&`, `*`, `<`, `>`, `(`, `)`, `{`, `}`, `[`, `]`,  `|`. El código de moneda XAH (todo en mayúsculas) está reservado para XAH y no se puede utilizar para tokens.
4. Los siguientes 40 bits están reservados y deben ser todos `0`'s.

El **formato no estándar** es cualquier dato de 160 bits siempre que los primeros 8 bits no sean `0x00`.

#### Campos Array

Algunos campos de transacción, como `SignerEntries` (en \[transacciones SignerListSet]\[]) y `Memos`, son arrays de objetos (llamadas tipo "STArray").

Los arrays contienen varios campos de objeto en su formato binario nativo en un orden específico. En JSON, cada miembro del array es un "wrapper" de objeto JSON con un solo campo, que es el nombre del campo del objeto miembro. El valor de ese campo es el objeto ("interno") en sí.

En el formato binario, cada miembro del array tiene un prefijo de ID de Campo (basado en la única clave del objeto wrapper) y contenidos (que comprenden el objeto interno, serializado como un objeto). Para marcar el final de un array, se agrega un elemento con un "ID de Campo" de `0xf1` (el código de tipo para el array con código de campo de 1) y sin contenidos.

El siguiente ejemplo muestra el formato de serialización para un array (el campo `SignerEntries`):

\{{ include\_svg("img/serialization-array.svg", 'ID de campo del array, seguido por el ID de Campo y contenidos de cada elemento del array, seguido por el ID de campo de "Fin del array"') \}}

#### Campos Blob

El tipo Blob es un campo prefijado con longitud que contiene datos arbitrarios. Dos campos comunes que utilizan este tipo son `SigningPubKey` y `TxnSignature`, que contienen (respectivamente) la clave pública y la firma que autorizan una transacción para ser ejecutada.

Los campos Blob no tienen una estructura adicional en sus contenidos, por lo que consisten exactamente en la cantidad de bytes indicada en la codificación de longitud variable, después del prefijo de ID de campo y los prefijos de longitud.

#### Campos Hash

Xahau tiene varios tipos de "hash": Hash128, Hash160 y Hash256. Estos campos contienen datos binarios arbitrarios del número dado de bits, que pueden o no representar el resultado de una operación de hash.

Todos esos campos se serializan como el número específico de bits, sin un indicador de longitud, en orden de bytes big-endian.

#### Campos Issue

Algunos campos especifican un _tipo_ de activo, que podría ser XAH o un token fungible, sin una cantidad. Estos campos consisten en uno o dos segmentos de 160 bits en orden:

1. Los primeros 160 bits son el código de moneda del activo. Para XAH, esto es todo 0's.
2. Si los primeros 160 bits son todos 0's (el activo es XAH), el campo termina ahí. De lo contrario, el activo es un token y los siguientes 160 bits son el ID de cuenta del emisor del token.

#### Campos Object

Algunos campos, como `SignerEntry` (en \[transacciones SignerListSet]\[]) y `Memo` (en arrays `Memos`) son objetos (llamados tipo "STObject"). La serialización de objetos es muy similar a la de matrices, con una diferencia: **los miembros del objeto deben colocarse en orden canónico** dentro del campo de objeto, donde los campos de matriz tienen un orden explícito ya establecido.

El orden de campo canónico de los campos de objeto es el mismo que el orden de campo canónico para todos los campos de nivel superior, pero los miembros del objeto deben ordenarse dentro del objeto. Después del último miembro, hay un ID de Campo de "Fin de Objeto" de `0xe1` sin contenidos.

El siguiente ejemplo muestra el formato de serialización para un objeto (un solo objeto `Memo` en la array `Memos`).

\{{ include\_svg("img/serialization-object.svg", 'ID de campo del objeto, seguido por el ID de Campo y contenidos de cada miembro del objeto en orden canónico, seguido por el ID de campo de "Fin de Objeto"') \}}

#### Campos PathSet

El campo `Paths` de una \[transacción de Pago]\[] entre divisas es un "PathSet", representado en JSON como un array de arrays. Para obtener más información sobre para qué se utilizan las rutas, consulta Paths.

Un PathSet se serializa como **1 a 6** rutas individuales en secuencia [\[Fuente\]](https://github.com/XRPLF/rippled/blob/4cff94f7a4a05302bdf1a248515379da99c5bcd4/src/ripple/app/tx/impl/Payment.h#L35-L36). Cada ruta completa es seguida por un byte que indica lo que viene a continuación:

* `0xff` indica que sigue otra ruta
* `0x00` indica el final del PathSet

Cada ruta consta de **1 a 8** pasos de ruta en orden [\[Fuente\]](https://github.com/XRPLF/rippled/blob/4cff94f7a4a05302bdf1a248515379da99c5bcd4/src/ripple/app/tx/impl/Payment.h#L38-L39). Cada paso comienza con un byte de **tipo**, seguido por uno o más campos que describen el paso de la ruta. El tipo indica qué campos están presentes en ese paso de ruta a través de flags bitwise. (Por ejemplo, el valor `0x30` indica cambiar tanto la divisa como el emisor.) Si hay más de un campo presente, los campos siempre se colocan en un orden específico.

La siguiente tabla describe los campos posibles y las banderas bitwise que se deben configurar en el byte de tipo para indicarlos:

| Tipo de Flag | Campo Presente | Tipo Campo    | Tamaño de Bits | Orden |
| --------- | ------------- | ------------- | -------- | ----- |
| `0x01`    | `account`     | AccountID     | 160 bits | 1st   |
| `0x10`    | `currency`    | Currency Code | 160 bits | 2nd   |
| `0x20`    | `issuer`      | AccountID     | 160 bits | 3rd   |

Algunas combinaciones son inválidas; consulta las Especificaciones de Rutas para obtener detalles.

Las AccountIDs en los campos `account` y `issuer` se presentan _sin_ un prefijo de longitud. Cuando la `currency` es XAH, el código de divisa se representa como 160 bits de ceros.

Cada paso es seguido directamente por el siguiente paso de la ruta. Como se describió anteriormente, el último paso de una ruta es seguido por `0xff` (si sigue otra ruta) o `0x00` (si esto termina la última ruta).

El siguiente ejemplo muestra el formato de serialización para un PathSet:

\{{ include\_svg("img/serialization-pathset.svg", "PathSet es varias rutas seguidas de un byte de continuar o finalizar; cada ruta es varios pasos de ruta que consisten en un byte de tipo y uno o más campos de 160 bits basados en el byte de tipo") \}}

#### Campos UInt

Xahau tiene varios tipos de enteros sin signo: UInt8, UInt16, UInt32 y UInt64. Todos estos son enteros binarios sin signo estándar en big-endian con el número de bits especificado.

Al representar estos campos en objetos JSON, la mayoría se representan como números JSON por defecto. Una excepción es UInt64, que se representa como una cadena porque algunos decodificadores JSON pueden intentar representar estos enteros como números de punto flotante de "doble precisión" de 64 bits, que no pueden representar todos los valores distintos de UInt64 con plena precisión.

Otro caso especial es el campo `TransactionType`. En JSON, este campo se representa convencionalmente como una cadena con el nombre del tipo de transacción, pero en binario, este campo es un UInt16. El objeto `TRANSACTION_TYPES` en el archivo de definiciones asigna estas cadenas a valores numéricos específicos.
