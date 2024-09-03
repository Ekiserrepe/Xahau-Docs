# Escrow

[\[Fuente\]](https://github.com/ripple/rippled/blob/c6b6d82a754fe449cc533e18659df483c10a5c98/src/ripple/protocol/impl/LedgerFormats.cpp#L90-L101)

_(Añadido por la \[enmienda Escrow]\[].)_

El tipo de objeto `Escrow` representa un pago retenido de XAH o IOU que está esperando ser ejecutado o cancelado. Una transacción de \[EscrowCreate]\[] crea un objeto `Escrow` en el libro mayor. Una transacción exitosa de \[EscrowFinish]\[] o \[EscrowCancel]\[] elimina el objeto.&#x20;

Si el objeto `Escrow` tiene una [_crypto-condición_](https://tools.ietf.org/html/draft-thomas-crypto-conditions-02), el pago solo puede tener éxito si una transacción de EscrowFinish proporciona el _fulfillment_ correspondiente que satisface la condición. (El único tipo de crypto-condición soportado es [PREIMAGE-SHA-256](https://tools.ietf.org/html/draft-thomas-crypto-conditions-02#section-8.1).) Si el objeto `Escrow` tiene un tiempo `FinishAfter`, el pago retenido solo puede ejecutarse después de ese tiempo.

Un objeto `Escrow` está asociado con dos direcciones:

* El propietario, quien proporciona el XAH o IOU al crear el objeto `Escrow`. Si el pago retenido es cancelado, el XAH o IOU regresa al propietario.
* El destinatario, donde el XAH o IOU se paga cuando el pago retenido tiene éxito. El destinatario puede ser el mismo que el propietario.

### Ejemplo JSON

```json
{
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "Amount": "10000",
    "CancelAfter": 545440232,
    "Condition": "A0258020A82A88B2DF843A54F58772E4A3861866ECDB4157645DD9AE528C1D3AEEDABAB6810120",
    "Destination": "ra5nK24KXen9AHvsdFTKHSANinZseWnPcX",
    "DestinationTag": 23480,
    "FinishAfter": 545354132,
    "Flags": 0,
    "LedgerEntryType": "Escrow",
    "OwnerNode": "0000000000000000",
    "DestinationNode": "0000000000000000",
    "PreviousTxnID": "C44F2EB84196B9AD820313DBEBA6316A15C9A2D35787579ED172B87A30131DA7",
    "PreviousTxnLgrSeq": 28991004,
    "SourceTag": 11747,
    "index": "DC5F3851D8A1AB622F957761E5963BC5BD439D5C24AC6AD7AC4523F0640244AC"
}
```

### Campos

Un objeto `Escrow` tiene los siguientes campos:

| Nombre                | Tipo JSON        | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                                                                                                                                                     |
| ------------------- | ---------------- | ------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Account`           | String           | AccountID           | Yes       | La dirección del propietario (remitente) de este pago retenido. Esta es la cuenta que proporcionó la cantidad y la recupera si el pago retenido es cancelado.                                                                                                                         |
| `Amount`            | String or Object | Amount              | Yes       | La cantidad a ser entregada por el pago retenido.                                                                                                                                                                                                                                 |
| `CancelAfter`       | Number           | UInt32              | No        | El pago retenido puede ser cancelado si y solo si este campo está presente _y_ el tiempo que especifica ha pasado. Específicamente, esto se especifica como \[segundos desde la Ripple Epoch]\[] y "ha pasado" si es anterior al tiempo de cierre del libro mayor validado anterior. |
| `Condition`         | String           | Blob                | No        | Una [crypto-condición PREIMAGE-SHA-256](https://tools.ietf.org/html/draft-thomas-crypto-conditions-02#section-8.1), en formato hexadecimal. Si está presente, la \[transacción EscrowFinish]\[] debe contener un fulfillment que satisfaga esta condición.                                      |
| `Destination`       | String           | AccountID           | Yes       | La dirección de destino donde se paga la cantidad si el pago retenido es exitoso.                                                                                                                                                                                             |
| `DestinationNode`   | String           | UInt64              | No        | Un indicio que señala qué página del directorio del propietario del destino enlaza a este objeto, en caso de que el directorio consista en varias páginas. Omitido en escrows creados antes de habilitar la \[enmienda fix1523]\[].                                                               |
| `DestinationTag`    | Number           | UInt32              | No        | Una etiqueta arbitraria para especificar más el destino de este pago retenido, como un receptor alojado en la dirección de destino.                                                                                                                                               |
| `FinishAfter`       | Number           | UInt32              | No        | El tiempo, en \[segundos desde la Ripple Epoch]\[], después del cual este pago retenido puede ser finalizado. Cualquier \[transacción EscrowFinish]\[] antes de este tiempo fallará. (Específicamente, esto se compara con el tiempo de cierre del ledger validado anterior.)                             |
| `Flags`             | Number           | UInt32              | Yes       | Un mapa de bits de flags booleanas habilitadas para este objeto. Actualmente, el protocolo no define ninguna bandera para los objetos `Escrow`. El valor es siempre `0`.                                                                                                                                     |
| `LedgerEntryType`   | String           | UInt16              | Yes       | El valor `0x0075`, asignado a la cadena `Escrow`, indica que este objeto es un objeto `Escrow`.                                                                                                                                                                            |
| `OwnerNode`         | String           | UInt64              | Yes       | Un indicio que señala qué página del directorio del propietario enlaza a este objeto, en caso de que el directorio consista en varias páginas. **Nota:** El objeto no contiene un enlace directo al directorio del propietario que lo contiene, ya que ese valor se puede derivar del `Account`.       |
| `PreviousTxnID`     | String           | Hash256             | Yes       | El hash identificador de la transacción que modificó más recientemente este objeto.                                                                                                                                                                                                |
| `PreviousTxnLgrSeq` | Number           | UInt32              | Yes       | El \[índice del ledger]\[Ledger Index] que contiene la transacción que modificó más recientemente este objeto.                                                                                                                                                                |
| `SourceTag`         | Number           | UInt32              | No        | Una etiqueta arbitraria para especificar más el origen de este pago retenido, como un receptor alojado en la dirección del propietario.                                                                                                                                                        |
| `TransferRate`      | Number           | UInt32              | No        |                                                                                                                                                                                                                                                                                 |

### Formato de ID Escrow

El ID de un objeto `Escrow` es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio Escrow (`0x0075`)
* El AccountID del remitente de la \[transacción EscrowCreate]\[] que creó el objeto `Escrow`
* El número de secuencia de la \[transacción EscrowCreate]\[] que creó el objeto `Escrow`. Si la transacción EscrowCreate usó un Ticket, usa el valor `TicketSequence` en su lugar.
