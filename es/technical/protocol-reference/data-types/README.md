# Tipos de datos

Los diferentes tipos de objetos se identifican de manera única de diferentes formas:

Las cuentas se identifican por su Address (Dirección), por ejemplo, `"r9cZA1mLK5R5Am25ArfXFmqgNwjZgnfk59"`. Las direcciones siempre comienzan con "r". Muchos métodos de `rippled` también aceptan una representación hexadecimal.

Las transacciones se identifican por un Hash del formato binario de la transacción. También puedes identificar una transacción por su cuenta de envío y el Sequence Number (Número de Secuencia).

Cada ledger cerrado tiene un ledger index (índice de ledger) y un valor Hash. Al [Especificar ledgers][] puedes usar cualquiera de los dos.

### Direcciones

### Hashes

#### Prefijos Hash

[\[Fuente\]](https://github.com/XRPLF/rippled/blob/master/src/ripple/protocol/HashPrefix.h)

En muchos casos, Xahau prefija los datos binarios de un objeto con un código de 4 bytes antes de calcular su hash, de modo que los objetos de diferentes tipos tengan diferentes hashes, incluso si sus formatos binarios son iguales. Los códigos de 4 bytes existentes están estructurados como tres caracteres alfabéticos, codificados como ASCII, seguidos de un byte cero.

Algunos tipos de hash aparecen en solicitudes y respuestas de API. Otros solo se calculan como el primer paso para firmar un tipo de datos determinado o calcular un hash de nivel superior. La siguiente tabla muestra todos los prefijos de hash de 4 bytes que utiliza Xahau:

| Tipo de objeto                           | Campos API                       | Prefijo Hash (Hex) | Prefijo Hash (Texto) |
| ------------------------------------- | -------------------------------- | ----------------- | ------------------ |
| Propuesta de consenso                    | N/A                              | `0x50525000`      | `PRP\0`            |
| Versión de ledger                        | `ledger_hash`                    | `0x4C575200`      | `LWR\0`            |
| Datos de estado del ledger                     | `account_state` en la cabecera del ledger | `0x4D4C4E00`      | `MLN\0`            |
| Nodo interno de datos del ledger                | N/A                              | `0x4D494E00`      | `MIN\0`            |
| Nodo interno de datos del ledger (SHAMapv2)     | N/A                              | `0x494E5200`      | `INR\0`            |
| Reclamo de canal de pago                 | N/A                              | `0x434C4D00`      | `CLM\0`            |
| Transacción firmada                   | `hash` de transacciones           | `0x54584E00`      | `TXN\0`            |
| Transacción con metadatos             | N/A                              | `0x534E4400`      | `SND\0`            |
| Transacción sin firmar (Firma única) | N/A                              | `0x53545800`      | `STX\0`            |
| Transacción sin firmar (Multi-firma)  | N/A                              | `0x534D5400`      | `SMT\0`            |
| Voto de validación                       | N/A                              | `0x56414C00`      | `VAL\0`            |
| Manifiesto de validador                    | N/A                              | `0x4D414E00`      | `MAN\0`            |

Los IDs de los objetos del ledger se calculan de manera similar, pero utilizan un prefijo de 2 bytes llamado "clave de espacio" en lugar de un prefijo en la forma descrita aquí.

### Secuencia de cuenta

### Ledger Index (Índice de ledger)

#### Especificar Ledgers

Muchos métodos de API requieren que especifiques una instancia del ledger, considerando que los datos recuperados están actualizados hasta esa versión particular del ledger compartido. Los comandos que aceptan una versión del ledger funcionan de la misma manera. Hay tres formas en que puedes especificar qué ledger deseas usar:

1.  Especifica un ledger por su Índice de ledger en el parámetro ledger_index. Cada ledger cerrado tiene un índice de ledger que es 1 mayor que el ledger anterior. (El primer ledger tuvo un índice de ledger de 1).

    ```
     "ledger_index": 61546724
    ```
2.  Especifica un ledger por su valor Hash en el parámetro `ledger_hash`.

    ```
     "ledger_hash": "8BB204CE37CFA7A021A16B5F6143400831C4D1779E6FE538D9AC561ABBF4A929"
    ```
3.  Especifica un ledger utilizando uno de los siguientes atajos en el parámetro `ledger_index`:

   * `validated` para el ledger más reciente que ha sido validado por consenso.

       ```
         "ledger_index": "validated"
       ```
   * `closed` para el ledger más reciente que ha sido cerrado para modificaciones y propuesto para validación.
   * `current` para la versión actual del ledger en la que está trabajando el servidor.

También existe un parámetro `ledger` en desuso que acepta cualquiera de los tres formatos anteriores. No uses este parámetro; podría ser eliminado sin previo aviso.

Si no especificas un ledger, el servidor decide cuál usar para atender la solicitud. Por defecto, el servidor elige el ledger `current` (en progreso). En Modo de Reporte, el servidor usa el ledger validado más reciente en su lugar. No proporciones más de un campo especificando ledgers.

**Nota:** No confíes en el comportamiento predeterminado para especificar un ledger; está sujeto a cambios. Siempre especifica una versión de ledger en la solicitud si puedes.

El Modo de Reporte no registra los datos del ledger hasta que se haya validado. Si realizas una solicitud a un servidor en Modo de Reporte para el ledger `current` o `closed`, el servidor reenvía la solicitud a un servidor en Modo P2P. Si solicitas un índice o hash de ledger que no está validado, un servidor en Modo de Reporte responde con un error `lgrNotFound`.

### Especificando cantidadesd de divisas

Hay dos tipos de divisas en Xahau: XAH y tokens. Estos dos tipos de divisas se especifican en diferentes formatos, con diferentes comportamientos de precisión y redondeo.

Algunos campos, como el `Amount` de destino de una [Transacción de Payment (Pago)][], pueden ser de cualquier tipo. Algunos campos solo aceptan específicamente XAH, como el campo `Fee` (tarifa de transacción).

XAH se especifica como una cadena que contiene un número entero de "drops" de XAH, donde 1 millón de drops equivale a 1 XAH. Los tokens se especifican en su lugar como un objeto con campos para la cantidad decimal, el código de divisa y el emisor. Por ejemplo:

*   **XAH** - Para especificar un campo `Amount` con un valor de 13.1 XAH:

    ```
      "Amount": "13100000"
    ```
*   **Token** - Para especificar un campo `Amount` con un valor de 13.1 FOO emitido por o para `rf1B...`:

    ```
      "Amount": {
          "value": "13.1",
          "currency": "FOO",
          "issuer": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn"
      }
    ```

Para más información, consultar Formatos de divisas.

### Especificando el tiempo

El servidor `xahaud` y sus APIs representan el tiempo como un número entero sin signo. Este número mide el número de segundos desde la "Ripple Epoch" del 1 de enero de 2000 (00:00 UTC). Esto es similar a la forma en que funciona la [Unix epoch](http://en.wikipedia.org/wiki/Unix\_time), excepto que la Ripple Epoch es 946684800 segundos después de la Unix Epoch.

No conviertas tiempos de la Ripple Epoch a tiempos de la Unix Epoch en variables de 32 bits: esto podría provocar desbordamientos de enteros.
