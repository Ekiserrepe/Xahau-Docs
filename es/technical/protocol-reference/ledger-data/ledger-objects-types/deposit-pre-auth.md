# Deposit Pre Auth

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/impl/LedgerFormats.cpp#L172-L178)

Un objeto `DepositPreauth` rastrea una preautorización de una cuenta a otra. Las \[transacciones DepositPreauth]\[] crean estos objetos.

Esto no tiene ningún efecto en el procesamiento de transacciones a menos que la cuenta que proporcionó la preautorización requiera autorización de depósito. En ese caso, la cuenta que fue preautorizada puede enviar pagos y otras transacciones directamente a la cuenta que proporcionó la preautorización. Las preautorizaciones son unidireccionales y no tienen efecto en los pagos en la dirección opuesta.

### Ejemplo JSON

```json
{
  "LedgerEntryType": "DepositPreauth",
  "Account": "rsUiUMpnrgxQp24dJYZDhmV4bE3aBtQyt8",
  "Authorize": "rEhxGqkqPPSxQ3P25J66ft5TwpzV14k2de",
  "Flags": 0,
  "OwnerNode": "0000000000000000",
  "PreviousTxnID": "3E8964D5A86B3CD6B9ECB33310D4E073D64C865A5B866200AD2B7E29F8326702",
  "PreviousTxnLgrSeq": 7,
  "index": "4A255038CC3ADCC1A9C91509279B59908251728D0DAADB248FFE297D0F7E068C"
}
```

### Campos

Un objeto `DepositPreauth` tiene los siguientes campos:

| Campo               | Tipo JSON | \[Tipo Interno]\[] | ¿Necesario? | Descripción                                                                                                                                                                                                                                                                        |
| ------------------- | --------- | ------------------- | --------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Account`           | String    | Account             | Sí       | La cuenta que otorgó la preautorización. (El destino de los pagos preautorizados).                                                                                                                                                                                    |
| `Authorize`         | String    | Account             | Sí       | La cuenta que recibió la preautorización. (El remitente de los pagos preautorizados).                                                                                                                                                                                        |
| `Flags`             | Number    | UInt32              | Sí       | Un mapa de bits de flags booleanos habilitados para este objeto. Actualmente, el protocolo no define ningún flag para los objetos `DepositPreauth`. El valor es siempre `0`.                                                                                                                                |
| `LedgerEntryType`   | String    | UInt16              | Sí       | El valor `0x0070`, mapeado en el string `DepositPreauth`, indica que este es un objeto de tipo DepositPreauth.                                                                                                                                                                         |
| `OwnerNode`         | String    | UInt64              | Sí       | Una pista que indica qué página del directorio de propietarios del remitente enlaza a este objeto, en caso de que el directorio consista en múltiples páginas. **Nota:** El objeto no contiene un enlace directo al directorio del propietario que lo contiene, ya que ese valor se puede derivar del `Account`. |
| `PreviousTxnID`     | String    | Hash256             | Sí       | El hash identificador de la transacción que más recientemente modificó este objeto.                                                                                                                                                                                                   |
| `PreviousTxnLgrSeq` | Number    | UInt32              | Sí       | El \[índice del ledger]\[Ledger Index] que contiene la transacción que más recientemente modificó este objeto.                                                                                                                                                                   |

### Formato ID DepositPreauth

El ID de un objeto `DepositPreauth` es el \[SHA-512Half]\[] de los siguientes valores, concatenados en orden:

* La clave de espacio DepositPreauth (`0x0070`)
* El AccountID del propietario de este objeto (el remitente de la \[transacción DepositPreauth]\[] que creó este objeto; en otras palabras, el que otorgó la preautorización)
* El AccountID de la cuenta preautorizada (el campo `Authorized` de la \[transacción DepositPreauth]\[] que creó este objeto; en otras palabras, el que recibió la preautorización)
