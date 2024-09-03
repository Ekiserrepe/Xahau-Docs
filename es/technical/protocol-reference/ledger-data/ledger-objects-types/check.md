# Check

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/impl/LedgerFormats.cpp#L157-L170)

_(Añadido por la \[enmienda Checks]\[].)_

Un objeto `Check` describe un cheque, similar a un cheque personal en papel, que puede ser cobrado por su destinatario para obtener dinero de su remitente. (El pago potencial ya ha sido aprobado por su remitente, pero no se mueve dinero hasta que se cobra. A diferencia de un Escrow, el dinero para un cheque no está reservado, por lo que cobrar el cheque podría fallar debido a falta de fondos).

### Ejemplo JSON

```json
{
  "Account": "rUn84CUYbNjRoTQ6mSW7BVJPSVJNLb1QLo",
  "Destination": "rfkE1aSy9G8Upk4JssnwBxhEv5p4mn2KTy",
  "DestinationNode": "0000000000000000",
  "DestinationTag": 1,
  "Expiration": 570113521,
  "Flags": 0,
  "InvoiceID": "46060241FABCF692D4D934BA2A6C4427CD4279083E38C77CBE642243E43BE291",
  "LedgerEntryType": "Check",
  "OwnerNode": "0000000000000000",
  "PreviousTxnID": "5463C6E08862A1FAE5EDAC12D70ADB16546A1F674930521295BC082494B62924",
  "PreviousTxnLgrSeq": 6,
  "SendMax": "100000000",
  "Sequence": 2,
  "index": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0"
}
```

### Campos

Un objeto `Check` tiene los siguientes campos:

| Campo               | Tipo JSON        | \[Tipo Interno]\[] | ¿Necesario? | Descripción                                                                                                                                                                                                                                                                        |
| ------------------- | ---------------- | ------------------- | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Account`           | String           | Account             | Sí       | El remitente del cheque. Cobrar el cheque cobra el saldo de esta dirección.                                                                                                                                                                                                          |
| `Destination`       | String           | Account             | Sí       | El destinatario previsto del cheque. Solo esta dirección puede cobrar el cheque, usando una \[transacción CheckCash]\[].                                                                                                                                                                    |
| `DestinationNode`   | String           | UInt64              | No        | Una pista que indica qué página del directorio de propietarios del destinatario enlaza a este objeto, en caso de que el directorio consista en múltiples páginas.                                                                                                                                          |
| `DestinationTag`    | Number           | UInt32              | No        | Una etiqueta arbitraria para especificar más el destino de este cheque, como un receptor alojado en la dirección del destinatario.                                                                                                                                                         |
| `Expiration`        | Number           | UInt32              | No        | Indica el tiempo después del cual este cheque se considera expirado. Ver \[Especificación de Tiempo]\[] para más detalles.                                                                                                                                                                            |
| `Flags`             | Number           | UInt32              | Sí       | Un mapa de bits de flags booleanos habilitados para este objeto. Actualmente, el protocolo no define ningún flag para los objetos `Check`. El valor es siempre `0`.                                                                                                                                         |
| `InvoiceID`         | String           | Hash256             | No        | Hash arbitrario de 256 bits proporcionado por el remitente como una razón o identificador específico para este cheque.                                                                                                                                                                                   |
| `LedgerEntryType`   | String           | UInt16              | Sí       | El valor `0x0043`, mapeado a la cadena `Check`, indica que este objeto es un objeto de tipo cheque.                                                                                                                                                                                    |
| `OwnerNode`         | String           | UInt64              | Sí       | Una pista que indica qué página del directorio de propietarios del remitente enlaza a este objeto, en caso de que el directorio consista en múltiples páginas. **Nota:** El objeto no contiene un enlace directo al directorio del propietario que lo contiene, ya que ese valor se puede derivar del `Account`. |
| `PreviousTxnID`     | String           | Hash256             | Sí       | El hash identificador de la transacción que más recientemente modificó este objeto.                                                                                                                                                                                                   |
| `PreviousTxnLgrSeq` | Number           | UInt32              | Sí       | El \[índice del ledger]\[Ledger Index] que contiene la transacción que más recientemente modificó este objeto.                                                                                                                                                                   |
| `SendMax`           | String or Object | Amount              | Sí       | La cantidad máxima de divisa que este cheque puede cobrar del remitente. Si el cheque se cobra con éxito, el destinatario es acreditado en la misma divisa por hasta esta cantidad.                                                                                                       |
| `Sequence`          | Number           | UInt32              | Sí       | El número de secuencia de la \[transacción CheckCreate]\[] que creó este cheque.                                                                                                                                                                                                  |
| `SourceTag`         | Number           | UInt32              | No        | Una etiqueta arbitraria para especificar más el origen de este cheque, como un receptor alojado en la dirección del remitente.                                                                                                                                                                 |

### Formato ID del Check

El ID de un objeto `Check` es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave espacio del Check (`0x0043`)
* El AccountID del remitente de la \[transacción CheckCreate]\[] que creó el objeto`Check`
* El número de `Sequence` de la \[transacción CheckCreate]\[] que creó el objeto `Check`. Si la transacción CheckCreate usó un Ticket, se usa el valor de `TicketSequence` en su lugar.
