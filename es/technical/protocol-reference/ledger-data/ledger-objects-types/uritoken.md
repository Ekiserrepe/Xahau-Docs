# URIToken

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/impl/LedgerFormats.cpp#L157-L170)

_(Añadido por la \[enmienda URI Token]\[].)_

Un objeto `URIToken` describe un token URI, que se puede usar para representar un identificador de recurso único en el ledger.

### Ejemplo JSON

```json
{
  "Owner": "rUn84CUYbNjRoTQ6mSW7BVJPSVJNLb1QLo",
  "OwnerNode": "0000000000000000",
  "Issuer": "rfkE1aSy9G8Upk4JssnwBxhEv5p4mn2KTy",
  "URI": "DEADBEEF",
  "Digest": "46060241FABCF692D4D934BA2A6C4427CD4279083E38C77CBE642243E43BE291",
  "Amount": "100000000",
  "Destination": "rUn84CUYbNjRoTQ6mSW7BVJPSVJNLb1QLo",
  "PreviousTxnID": "5463C6E08862A1FAE5EDAC12D70ADB16546A1F674930521295BC082494B62924",
  "PreviousTxnLgrSeq": 6,
  "LedgerEntryType": "URIToken",
  "index": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0"
}
```

### Campos

Un objeto `URIToken` tiene los siguientes campos:

| Campo               | Tipo JSON        | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                   |
| ------------------- | ---------------- | ------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------- |
| `Owner`             | String           | Account             | Sí       | El propietario del token URI.                                                                                                   |
| `OwnerNode`         | String           | UInt64              | Sí       | Una pista que indica qué página del directorio del propietario enlaza a este objeto, en caso de que el directorio consista de varias páginas. |
| `Issuer`            | String           | Account             | Sí       | El emisor del token URI.                                                                                                  |
| `URI`               | String           | VL                  | Sí       | El URI representado por este token.                                                                                            |
| `Digest`            | String           | Hash256             | No        | Hash arbitrario de 256 bits proporcionado por el propietario como un identificador específico para este token URI.                                     |
| `Amount`            | String or Object | Amount              | No        | La cantidad del token URI.                                                                                                  |
| `Destination`       | String           | Account             | No        | El destinatario previsto del token URI.                                                                                      |
| `PreviousTxnID`     | String           | Hash256             | Sí       | El hash identificador de la transacción que más recientemente modificó este objeto.                                              |
| `PreviousTxnLgrSeq` | Number           | UInt32              | Sí       | El índice del ledger que contiene la transacción que más recientemente modificó este objeto.                                |
| `LedgerEntryType`   | String           | UInt16              | Sí       | El valor `0x0073`, mapeado al string `URIToken`, indica que este objeto es un objeto URI Token.                        |

### Flags de URIToken

Los objetos `URIToken` pueden tener los siguientes valores de flags:

| Nombre del Flag     | Valor Hex    | Valor Decimal | Flag Correspondiente URITokenMint | Descripción                                                                                                                       |
| ------------- | ------------ | ------------- | ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------- |
| `lsfBurnable` | `0x00000001` | 1             | `tfBurnable`                    | Permite al emisor (o a una entidad autorizada por el emisor) destruir el `URIToken` emitido. (El propietario del `URIToken` _siempre_ puede hacerlo). |

### Formato de ID de URIToken

El ID de un objeto `URIToken` es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio del token URI (`0x0055`)
* El AccountID del emisor del token URI
* El URI representado por el token URI
