# Campos Comunes de Transacciones

Cada transacción tiene un conjunto de campos comunes, además de campos adicionales según el tipo de transacción. Los nombres de los campos distinguen entre mayúsculas y minúsculas. Los campos comunes para todas las transacciones son:

| Campo                | Tipo JSON        | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                                                                                                                                |
| -------------------- | ---------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Account`            | String           | AccountID           | _(Requerido)_ La dirección única de la cuenta que inició la transacción.                                                                                                                                                                                                                                                             |
| `TransactionType`    | String           | UInt16              | _(Requerido)_ El tipo de transacción. Los tipos de transacción válidos incluyen: `Payment`, `OfferCreate`, `TrustSet`, entre muchos otros.                                                                                                                                                                                                              |
| `Fee`                | String           | Amount              | _(Requerido; auto-completable)_ Cantidad entera de XAH, en drops, que se destruirá como coste por distribuir esta transacción en la red. Algunos tipos de transacciones tienen diferentes requisitos mínimos. Ver \[Coste de Transacción]\[] para más detalles.                                                                                               |
| `Sequence`           | Number           | UInt32              | _(Requerido; auto-completable)_ El número de secuencia de la cuenta que envía la transacción. Una transacción solo es válida si el número de `Sequence` es exactamente 1 mayor que la transacción anterior de la misma cuenta. El caso especial `0` significa que la transacción está utilizando un Ticket en su lugar _(Agregado por la \[enmienda TicketBatch]\[])_. |
| `AccountTxnID`       | String           | Hash256             | _(Opcional)_ Valor hash que identifica otra transacción. Si se proporciona, esta transacción solo es válida si la transacción enviada previamente por la cuenta coincide con el hash proporcionado.                                                                                                                                                       |
| `Flags`              | Number           | UInt32              | _(Opcional)_ Conjunto de flags de bits para esta transacción.                                                                                                                                                                                                                                                                                        |
| `LastLedgerSequence` | Number           | UInt32              | _(Opcional; altamente recomendado)_ Índice de ledger más alto en el que puede aparecer esta transacción. Especificar este campo establece un límite estricto sobre cuánto tiempo puede esperar la transacción para ser validada o rechazada. Ver Envío de Transacciones Confiable para más detalles.                                                                          |
| `Memos`              | Array of Objects | Array               | _(Opcional)_ Información adicional arbitraria utilizada para identificar esta transacción.                                                                                                                                                                                                                                                           |
| `NetworkID`          | Number           | UInt32              | _(Específico de la red)_ El ID de red de la cadena para la que está destinada esta transacción. **DEBE OMITIRSE** para Mainnet y algunas redes de prueba. **REQUERIDO** en cadenas cuyo ID de red sea 1025 o superior.                                                                                                                                      |
| `Signers`            | Array            | Array               | _(Opcional)_ Array de objetos que representan una firma múltiple que autoriza esta transacción.                                                                                                                                                                                                                                          |
| `SourceTag`          | Number           | UInt32              | _(Opcional)_ Entero arbitrario utilizado para identificar el motivo de este pago, o un remitente en cuyo nombre se realiza esta transacción. Convencionalmente, un reembolso debe especificar el `SourceTag` del pago inicial como el `DestinationTag` del pago de reembolso.                                                                                     |
| `SigningPubKey`      | String           | Blob                | _(Agregado automáticamente al firmar)_ Representación hexadecimal de la clave pública que corresponde a la clave privada utilizada para firmar esta transacción. Si es una string vacío, indica que hay una firma múltiple presente en el campo `Signers`.                                                                                                    |
| `TicketSequence`     | Number           | UInt32              | _(Opcional)_ El número de secuencia del ticket que se utilizará en lugar de un número de `Sequence`. Si se proporciona, `Sequence` debe ser `0`. No se puede utilizar con `AccountTxnID`.                                                                                                                                                                    |
| `TxnSignature`       | String           | Blob                | _(Agregado automáticamente al firmar)_ La firma que verifica esta transacción como originada desde la cuenta que dice ser.                                                                                                                                                                                                      |
| `EmitDetails`        | Object           | Object              | Contiene detalles sobre la emisión. Esto incluye la generación de la emisión, la carga de la emisión, la dirección de callback, el hash del hook que emitió la transacción, el nonce de la emisión y el ID de la transacción principal.                                                                                   |
| `HookParameters`     | Array            | Array               | Los parámetros del gancho de la transacción.                                                                                                                                                                                                                                                                                                             |

\[Eliminado en: rippled 0.28.0]\[]: El campo `PreviousTxnID` de las transacciones fue reemplazado por el campo `AccountTxnID`. Este campo String / Hash256 está presente en algunas transacciones históricas. Esto no está relacionado con el campo también llamado `PreviousTxnID` en algunos objetos del libro mayor.

### AccountTxnID

El campo `AccountTxnID` te permite encadenar tus transacciones, de modo que una transacción actual no sea válida a menos que la transacción anterior enviada desde la misma cuenta tenga un \[hash de transacción]\[hash identificador] específico.

A diferencia del campo `PreviousTxnID`, que rastrea la última transacción que _modificó_ una cuenta (independientemente del remitente), el `AccountTxnID` rastrea la última transacción _enviada por_ una cuenta. Para usar `AccountTxnID`, primero debes habilitar el flag `asfAccountTxnID`, para que el libro mayor rastree el ID de la transacción anterior de la cuenta. (`PreviousTxnID`, en comparación, siempre se rastrea).

Una situación en la que esto es útil es si tienes un sistema primario para enviar transacciones y un sistema de respaldo pasivo. Si el sistema de respaldo pasivo se desconecta del primario, pero el primario no está completamente muerto, y ambos comienzan a operar al mismo tiempo, podrías tener problemas graves como que algunas transacciones se envíen dos veces y otras no se envíen en absoluto. Encadenar tus transacciones con `AccountTxnID` asegura que, incluso si ambos sistemas están activos, solo uno de ellos puede enviar transacciones válidas a la vez.

El campo `AccountTxnID` no se puede utilizar en transacciones que utilizan Tickets. Las transacciones que utilizan `AccountTxnID` no pueden colocarse en la cola de transacciones.

### Campos Auto-completables

Algunos campos se pueden completar automáticamente antes de que se firme una transacción, ya sea por un servidor `xahaud` o por una biblioteca cliente. Completar automáticamente los valores requiere una conexión activa a Xahau para obtener el estado más reciente, por lo que no se puede hacer sin conexión. Los detalles pueden variar según la librería, pero la auto-completación siempre proporciona valores adecuados para al menos los siguientes campos:

*   `Fee` - Completa automáticamente el \[Coste de Transacción]\[] según la red.

    **Nota:** Al usar el \[comando sign]\[] de xahaud, puedes limitar el valor máximo posible auto-completado, utilizando los parámetros `fee_mult_max` y `fee_mult_div`.
* `Sequence` - Utiliza automáticamente el siguiente número de secuencia para la cuenta que envía la transacción.

Para un sistema de producción, recomendamos _no_ dejar estos campos para que los complete el servidor. Por ejemplo, si los costes de transacción se vuelven altos debido a un aumento temporal en la carga de la red, es posible que desees esperar a que el coste disminuya antes de enviar algunas transacciones, en lugar de pagar el costo temporalmente alto.

El campo `Paths` del tipo de \[transacción Payment]\[] también se puede completar automáticamente.

### Campo Flags

El campo `Flags` puede contener varias opciones que afectan cómo debe comportarse una transacción. Las opciones se representan como valores binarios que se pueden combinar con operaciones bitwise-or para establecer múltiples flags a la vez.

Para verificar si una transacción tiene un flag habilitado, utiliza el operador bitwise-and en el valor del flag y el campo `Flags`. Un resultado de cero indica que el flag está deshabilitado, y un resultado igual al valor del flag indica que el flag está habilitado. (Si obtuviste cualquier otro resultado, hiciste algo mal).

La mayoría de los flags solo tienen significado para un tipo específico de transacción. El mismo valor de bitwise puede reutilizarse para flags en diferentes tipos de transacciones, por lo que es importante prestar atención al campo `TransactionType` al configurar y leer flags.

Los bits que no están definidos como flags DEBEN ser 0. (La \[enmienda fix1543]\[] hace cumplir esta regla en algunos tipos de transacciones. La mayoría de los tipos de transacciones hacen cumplir esta regla de manera predeterminada).

#### Flags Globales

El único flag que se aplica globalmente a todas las transacciones es el siguiente:

| Nombre Flag             | Valor Hex    | Valor Decimal | Descripción                                                                                                                                 |
| --------------------- | ------------ | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------- |
| `tfFullyCanonicalSig` | `0x80000000` | 2147483648    | **DEPRECATED** Sin efecto. (Si la \[enmienda RequireFullyCanonicalSig]\[] no está habilitada, este flag obliga a una firma totalmente canónica.) |

Al usar el \[método sign]\[] (o el \[método submit]\[] en modo "sign-and-submit"), `xahaud` agrega un campo `Flags` con `tfFullyCanonicalSig` habilitado a menos que el campo `Flags` ya esté presente. El flag `tfFullyCanonicalSig` no se habilita automáticamente si `Flags` se especifica explícitamente. El flag no se habilita automáticamente al usar el \[método sign\_for]\[] para agregar una firma a una transacción firmada múltiples veces.

**Nota:** El flag `tfFullyCanonicalSig` se utilizó desde 2014 hasta 2020 para proteger contra la maleabilidad de transacciones mientras se mantenía la compatibilidad con el software de firma heredado. La \[enmienda RequireFullyCanonicalSig]\[] terminó con la compatibilidad con dicho software heredado e hizo que las protecciones fueran predeterminadas para todas las transacciones. Si estás utilizando una red paralela que no tiene RequireFullyCanonicalSig habilitado, siempre debes habilitar el flag `tfFullyCanonicalSig` para proteger contra la maleabilidad de transacciones.

#### Rangos de Flag

El campo `Flags` de una transacción puede contener flags que se aplican en diferentes niveles o contextos. Los flags para cada contexto están limitados a los siguientes rangos:

| Nombre Rango       | Mascara de Bits    | Descripción                                                                                    |
| ---------------- | ------------ | ---------------------------------------------------------------------------------------------- |
| Flags universales  | `0xff000000` | Flags que se aplican por igual a todos los tipos de transacciones.                                             |
| Flags basados en tipo | `0x00ff0000` | Flags con significados diferentes dependiendo del tipo de transacción que los utiliza.                |
| Flags reservados   | `0x0000ffff` | Flags que no están actualmente definidos. Una transacción solo es válida si estos flags están deshabilitados.
 |

**Nota:** El tipo de \[transacción AccountSet]\[] tiene sus propios flags no basados en bits, que cumplen una función similar a los flags basados en tipo. Los objetos del libro mayor también tienen un campo `Flags` con diferentes definiciones de flags basados en bits.

### Campo Memos

El campo `Memos` incluye datos de mensajería arbitrarios con la transacción. Se presenta como un array de objetos. Cada objeto tiene solo un campo, `Memo`, que a su vez contiene otro objeto con _uno o más_ de los siguientes campos:

| Campo        | Tipo   | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                       |
| ------------ | ------ | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `MemoData`   | String | Blob                | Valor hexadecimal arbitrario, convencionalmente contiene el contenido del memo.                                                                                                                                           |
| `MemoFormat` | String | Blob                | Valor hexadecimal que representa caracteres permitidos en URLs. Convencionalmente contiene información sobre cómo se codifica el memo, por ejemplo, como un [tipo MIME](http://www.iana.org/assignments/media-types/media-types.xhtml). |
| `MemoType`   | String | Blob                | Valor hexadecimal que representa caracteres permitidos en URLs. Convencionalmente, una relación única (según [RFC 5988](http://tools.ietf.org/html/rfc5988#section-4)) que define el formato de este memo.                |

Los campos `MemoType` y `MemoFormat` solo deben consistir en los siguientes caracteres: `ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789-._~:/?#[]@!$&'()*+,;=%`

El campo `Memos` está limitado a no más de 1 KB de tamaño (cuando se serializa en formato binario).

Ejemplo de una transacción con un campo Memos:

```json
{
    "TransactionType": "Payment",
    "Account": "rMmTCjGFRWPz8S2zAUUoNVSQHxtRQD4eCx",
    "Destination": "r3kmLJN5D28dHuH8vZNUZpMC43pEHpaocV",
    "Memos": [
        {
            "Memo": {
                "MemoType": "687474703a2f2f6578616d706c652e636f6d2f6d656d6f2f67656e65726963",
                "MemoData": "72656e74"
            }
        }
    ],
    "Amount": "1"
}
```

### Campo NetworkID

\[Nuevo en: rippled 1.11.0]\[]

El campo `NetworkID` es una protección contra ataques de repetición de transacciones entre cadenas, evitando que la misma transacción sea copiada y ejecutada en una red paralela para la que no estaba destinada. Para la compatibilidad con cadenas existentes, el campo `NetworkID` debe omitirse en cualquier red con un ID de Red de 1024 o menos, pero debe incluirse en cualquier red con un ID de Red de 1025 o superior. La siguiente tabla muestra el estado y los valores para varias redes conocidas:

| Network                         | ID    | Campo `NetworkID`                                    |
| ------------------------------- | ----- | ---------------------------------------------------- |
| Mainnet                         | 0     | No permitido                                           |
| Testnet                         | 1     | No permitido                                           |
| Devnet                          | 2     | No permitido                                           |
| AMM Devnet                      | 25    | No permitido                                           |
| Sidechains Devnet Locking Chain | 2551  | No permitido, pero será requerido después de una actualización
 |
| Sidechains Devnet Issuing Chain | 2552  | No permitido, pero será requerido después de una actualización
 |
| Xahau Testnet                   | 21338 | Requerido                                             |
| Xahau Mainnet                   | 21337 | Requerido                                             |

Los ataques de repetición de transacciones son teóricamente posibles, pero requieren condiciones específicas en la segunda red. Todas las siguientes deben ser verdaderas:

* El remitente de la transacción es una cuenta financiada en la segunda red.
* El número de `Sequence` del remitente en la segunda red coincide con el `Sequence` de la transacción, o la transacción utiliza un Ticket que está disponible en la segunda red.
* La transacción no tiene un campo `LastLedgerSequence`, o especifica un valor que es más alto que el índice del libro mayor actual en el segundo libro mayor.
  * Mainnet generalmente tiene un índice de ledger más alto que las redes de prueba o cadenas laterales, por lo que es más fácil repetir transacciones de Mainnet en una cadena lateral o red de prueba que al revés, cuando las transacciones utilizan `LastLedgerSequence` según lo previsto.
* Ambas redes tienen IDs de 1024 o menos, ambas redes utilizan el mismo ID, o la segunda red no requiere el campo `NetworkID`.

### Campo Signers

El campo `Signers` contiene una firma múltiple, que tiene firmas de hasta 8 pares de claves, que juntas deben autorizar la transacción. La lista de `Signers` es un array de objetos, cada uno con un campo, `Signer`. El campo `Signer` tiene los siguientes campos anidados:

| Campo           | Tipo   | \[Tipo Interno]\[] | Descripción                                                                   |
| --------------- | ------ | ------------------- | ----------------------------------------------------------------------------- |
| `Account`       | String | AccountID           | La dirección asociada con esta firma, como aparece en la lista de firmantes. |
| `TxnSignature`  | String | Blob                | Una firma para esta transacción, verificable usando la `SigningPubKey`.       |
| `SigningPubKey` | String | Blob                | La clave pública utilizada para crear esta firma.                                 |

La `SigningPubKey` debe ser una clave que esté asociada con la dirección `Account`. Si la dirección `Account` referenciada es una cuenta financiada en el libro mayor, entonces la `SigningPubKey` puede ser la Clave Regular actual de esa cuenta si está configurada. También podría ser la Clave Maestra de esa cuenta, a menos que el flag `lsfDisableMaster` esté habilitada. Si la dirección `Account` referenciada no es una cuenta financiada en el ledger, entonces la `SigningPubKey` debe ser la clave maestra asociada con esa dirección.

Debido a que la verificación de firmas es una tarea intensiva en computación, las transacciones firmadas múltiples cuestan XAH adicional para retransmitirse a la red. Cada firma incluida en la firma múltiple aumenta el \[coste de transacción]\[] requerido para la transacción. Por ejemplo, si el costo mínimo actual de transacción para retransmitir una transacción a la red es de `10000` drops, entonces una transacción firmada múltiples veces con 3 entradas en el array `Signers` necesitaría un valor de `Fee` de al menos `40000` drops para retransmitirse.

### Campos EmitDetails

Un objeto EmitDetails tiene los siguientes campos:

| Campo             | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                                                                                                                                                     |
| ----------------- | --------- | ------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `EmitGeneration`  | Number    | UInt32              | Sí       | Este campo realiza un seguimiento de una cadena de transacciones emitidas que a su vez causan que se emitan otras transacciones.                                                                                                                                                                  |
| `EmitBurden`      | String    | UInt64              | Sí       | Este campo es una heurística para detectar bombas fork. Las tarifas se basan en la carga y aumentarán exponencialmente cuando se inicie una reacción en cadena para evitar que la red se vea abrumada por transacciones emitidas que se refuerzan entre sí.                                                   |
| `EmitParentTxnID` | String    | Hash256             | Sí       | La Ejecución del Hook que emitió la transacción está conectada a la Transacción Originaria. Por lo tanto, este campo siempre es necesario para el rastreo eficiente del comportamiento.                                                                                                    |
| `EmitNonce`       | String    | Hash256             | Sí       | Las Transacciones Emitidas serían idénticas con los mismos campos y, por lo tanto, tendrían hashes de transacción idénticos si no se utilizara un nonce. Sin embargo, cada nodo en la red necesita acordar sobre el nonce, por lo que se hace disponible una API especial de Gancho para producir un nonce determinista. |
| `EmitCallback`    | String    | AccountID           | No        | Este campo es utilizado por xahld cuando necesita iniciar una devolución de llamada, de tal manera que sabe en qué Hook y cuenta iniciar la devolución de llamada. Las devoluciones de llamada ocurren cuando una transacción emitida es aceptada en un ledger.                                                                 |
| `EmitHookHash`    | String    | Hash256             | Sí       | El SHA512H del Hook en el momento en que se ejecutó.                                                                                                                                                                                                                            |

### Parámetros Hook

El campo `HookParameters` es un array de objetos que especifican los parámetros del gancho. Cada objeto de parámetro tiene los siguientes campos:

| Campo   | Tipo JSON | Tipo Interno | Descripción                 |
| ------- | --------- | ------------- | --------------------------- |
| `Name`  | String    | Blob          | El nombre del parámetro.  |
| `Value` | String    | Blob          | El valor del parámetro. |
