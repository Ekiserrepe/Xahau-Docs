# Pay Channel

[\[Fuente\]](https://github.com/ripple/rippled/blob/c0a0b79d2d483b318ce1d82e526bd53df83a4a2c/src/ripple/protocol/impl/LedgerFormats.cpp#L180-L198)

_(Añadido por la \[enmienda PayChan]\[].)_

El tipo de objeto `PayChannel` representa un canal de pago. Los canales de pago permiten pagos pequeños y rápidos fuera del ledger de XAH o IOU que luego pueden reconciliarse con el ledger de consenso. Un canal de pago mantiene un saldo de XAH o IOU que solo puede pagarse a una dirección de destino específica hasta que se cierre el canal. Cualquier XAH o IOU no gastado se devuelve al propietario del canal (la dirección de origen que lo creó y financió) cuando el canal se cierra.

El tipo de transacción \[PaymentChannelCreate]\[] crea un objeto `PayChannel`. Los tipos de transacciones \[PaymentChannelFund]\[] y \[PaymentChannelClaim]\[] modifican los objetos `PayChannel` existentes.

Cuando un canal de pago expira, inicialmente permanece en el ledger, porque solo las nuevas transacciones pueden modificar el contenido del ledger. El procesamiento de transacciones cierra automáticamente un canal de pago cuando cualquier transacción lo accede después de la expiración. Para cerrar un canal expirado y devolver el XAH o IOU no gastado al propietario, algunas direcciones deben enviar una nueva transacción PaymentChannelClaim o PaymentChannelFund que acceda al canal.

Para un ejemplo de uso de canales de pago, consulta el Tutorial de Canales de Pago.

### Ejemplo JSON

```json
{
    "Account": "rBqb89MRQJnMPq8wTwEbtz4kvxrEDfcYvt",
    "Destination": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "Amount": "4325800",
    "Balance": "2323423",
    "PublicKey": "32D2471DB72B27E3310F355BB33E339BF26F8392D5A93D3BC0FC3B566612DA0F0A",
    "SettleDelay": 3600,
    "Expiration": 536027313,
    "CancelAfter": 536891313,
    "SourceTag": 0,
    "DestinationTag": 1002341,
    "DestinationNode": "0000000000000000",
    "Flags": 0,
    "LedgerEntryType": "PayChannel",
    "OwnerNode": "0000000000000000",
    "PreviousTxnID": "F0AB71E777B2DA54B86231E19B82554EF1F8211F92ECA473121C655BFC5329BF",
    "PreviousTxnLgrSeq": 14524914,
    "index": "96F76F27D8A327FC48753167EC04A46AA0E382E6F57F32FD12274144D00F1797"
}
```

### Campos

Un objeto `PayChannel` tiene los siguientes campos:

| Nombre                | Tipo JSON        | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                                                                                                                                                                                                                                                                     |
| ------------------- | ---------------- | ------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Account`           | String           | AccountID           | Sí       | La dirección de origen que posee este canal de pago. Esto proviene de la dirección de envío de la transacción que creó el canal.                                                                                                                                                                                                                                                             |
| `Amount`            | String or Object | Amount              | Sí       | Cantidad total que se ha asignado a este canal. Esto incluye las cantidades que se han pagado a la dirección de destino. Se establece inicialmente con la transacción que creó el canal y se puede aumentar si la dirección de origen envía una transacción PaymentChannelFund.                                                                                                                |
| `Balance`           | String or Object | Amount              | Sí       | Cantidad total ya pagada por el canal. La diferencia entre este valor y el campo `Amount` es la cantidad que aún se puede pagar a la dirección de destino con transacciones PaymentChannelClaim. Si el canal se cierra, la diferencia restante se devuelve a la dirección de origen.                                                                                                |
| `CancelAfter`       | Number           | UInt32              | No        | El tiempo de expiración inmutable para este canal de pago, en \[segundos desde la Ripple Epoch]\. Este canal está expirado si este valor está presente y es menor que el campo `close_time` del ledger anterior. Esto se establece opcionalmente con la transacción que creó el canal y no se puede cambiar.                                                                                         |
| `Destination`       | String           | AccountID           | Sí       | La dirección de destino para este canal de pago. Mientras el canal de pago esté abierto, esta dirección es la única que puede recibir la cantidad del canal. Esto proviene del campo `Destination` de la transacción que creó el canal.                                                                                                                                            |
| `DestinationTag`    | Number           | UInt32              | No        | Una etiqueta arbitraria para especificar aún más el destino de este canal de pago, como un destinatario alojado en la dirección de destino.                                                                                                                                                                                                                                                            |
| `DestinationNode`   | String           | UInt64              | No        | Una pista que indica qué página del directorio del propietario de la dirección de destino enlaza con este objeto, en caso de que el directorio consista en varias páginas. Omitido en canales de pago creados antes de habilitar la \[enmienda fixPayChanRecipientOwnerDir]\[].                                                                                                                                                  |
| `Expiration`        | Number           | UInt32              | No        | El tiempo de expiración mutable para este canal de pago, en \[segundos desde la Ripple Epoch]\[]. El canal está expirado si este valor está presente y es menor que el campo `close_time` del ledger anterior. Ver Configuración de la Expiración del Canal para más detalles.                                                                                                                                      |
| `Flags`             | Number           | UInt32              | Sí       | Un mapa de bits de flags booleanos habilitados para este objeto. Actualmente, el protocolo no define flags para los objetos `PayChannel`. El valor siempre es `0`.                                                                                                                                                                                                                                                 |
| `LedgerEntryType`   | String           | UInt16              | Sí       | El valor `0x0078`, mapeado a la cadena `PayChannel`, indica que este objeto es un objeto de canal de pago.                                                                                                                                                                                                                                                                                  |
| `OwnerNode`         | String           | UInt64              | Sí       | Una pista que indica qué página del directorio del propietario de la dirección de origen enlaza con este objeto, en caso de que el directorio consista en varias páginas.                                                                                                                                                                                                                                                    |
| `PreviousTxnID`     | String           | Hash256             | Sí       | El hash identificador de la transacción que más recientemente modificó este objeto.                                                                                                                                                                                                                                                                                                                |
| `PreviousTxnLgrSeq` | Number           | UInt32              | Sí       | El \[índice del ledger]\[Ledger Index] que contiene la transacción que más recientemente modificó este objeto.                                                                                                                                                                                                                                                                                |
| `PublicKey`         | String           | Blob                | Sí       | Clave pública, en hexadecimal, del par de claves que se puede utilizar para firmar reclamos contra este canal. Esta puede ser cualquier clave pública válida de secp256k1 o Ed25519. Esta clave se establece con la transacción que creó el canal y debe coincidir con la clave pública utilizada en los reclamos contra el canal. La dirección de origen del canal también puede enviar montos de este canal a la dirección de destino sin reclamos firmados. |
| `SettleDelay`       | Number           | UInt32              | Sí       | Número de segundos que la dirección de origen debe esperar para cerrar el canal si todavía tiene algún saldo. Valores más pequeños significan que la dirección de destino tiene menos tiempo para redimir cualquier reclamo pendiente después de que la dirección de origen solicite cerrar el canal. Puede ser cualquier valor que se ajuste a un entero sin signo de 32 bits (0 a 2^32-1). Esto se establece con la transacción que crea el canal.   |
| `SourceTag`         | Number           | UInt32              | No        | Un tag arbitrario para especificar aún más la fuente de este canal de pago, como un destinatario alojado en la dirección del propietario.                                                                                                                                                                                                                                                                     |
| `TransferRate`      | Number           | UInt32              | No        |                                                                                                                                                                                                                                                                                                                                                                                                 |

### Configuración de la Expiración del Canal

El campo `Expiration` de un canal de pago es el tiempo de expiración mutable, en contraste con el tiempo de expiración inmutable representado por el campo `CancelAfter`. La expiración de un canal siempre se considera en relación con el campo `close_time` del ledger anterior. El campo `Expiration` se omite cuando se crea un objeto `PayChannel`. Hay varias formas en que se puede actualizar el campo `Expiration` de un objeto `PayChannel`, que se pueden resumir de la siguiente manera: la dirección de origen de un canal puede establecer libremente la `Expiration` del canal siempre que el canal permanezca abierto al menos `SettleDelay` segundos después del primer intento de cerrarlo.

#### Dirección de Origen

La dirección de origen puede establecer la `Expiration` directamente con el tipo de transacción PaymentChannelFund. El nuevo valor no debe ser anterior al valor más cercano de los siguientes:

* El valor actual de `Expiration` (si se establece uno).
* El tiempo de cierre del ledger anterior más el `SettleDelay` del canal.

En otras palabras, la dirección de origen siempre puede hacer que la `Expiration` sea más tarde si ya se ha establecido una expiración. La fuente puede hacer que un valor de `Expiration` sea anterior o establecer un `Expiration` si no se ha establecido actualmente, siempre que el nuevo valor sea al menos `SettleDelay` segundos en el futuro. Si la dirección de origen intenta establecer una fecha de `Expiration` no válida, la transacción falla con el código de error `temBAD_EXPIRATION`.

La dirección de origen también puede establecer la `Expiration` con el flag `tfClose` del tipo de transacción PaymentChannelClaim. Si el flag está habilitado, el ledger establece automáticamente la `Expiration` al valor más cercano de los siguientes:

* El valor actual de `Expiration` (si se establece uno).
* El tiempo de cierre del ledger anterior más el `SettleDelay` del canal.

La dirección de origen puede eliminar la `Expiration` con el flag `tfRenew` del tipo de transacción PaymentChannelClaim.

#### Dirección de Destino

La dirección de destino no puede establecer el campo `Expiration`. Sin embargo, la dirección de destino puede usar el flag `tfClose` de la transacción PaymentChannelClaim para cerrar un canal de inmediato.

#### Otras Direcciones

Si alguna otra dirección intenta establecer un campo de `Expiration`, la transacción falla con el código de error `tecNO_PERMISSION`. Sin embargo, si el canal ya está expirado, la transacción provoca el cierre del canal y da como resultado `tesSUCCESS`.

### Formato de ID de PayChannel

The ID de un objeto `PayChannel` es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio PayChannel (`0x0078`).
* El AccountID de la cuenta de origen.
* El AccountID de la cuenta de destino.
* El número de secuencia de la \[transacción PaymentChannelCreate]\[] que creó el canal. Si la transacción PaymentChannelCreate utilizó un Ticket, utiliza el valor `TicketSequence` en su lugar.
