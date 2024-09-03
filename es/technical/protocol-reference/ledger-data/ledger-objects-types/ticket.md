# Ticket

[\[Fuente\]](https://github.com/ripple/rippled/blob/76a6956138c4ecd156c5c408f136ed3d6ab7d0c1/src/ripple/protocol/impl/LedgerFormats.cpp#L155-L164)

_(Añadido por la \[enmienda TicketBatch]\[].)_

El tipo de objeto `Ticket` representa un Ticket, que rastrea un \[número de secuencia]\[Sequence Number] de cuenta que se ha reservado para uso futuro. Puedes crear nuevos tickets con una \[transacción TicketCreate]\[]. \[Nuevo en: rippled 1.7.0]\[]

### Ejemplo JSON

```json
{
  "Account": "rEhxGqkqPPSxQ3P25J66ft5TwpzV14k2de",
  "Flags": 0,
  "LedgerEntryType": "Ticket",
  "OwnerNode": "0000000000000000",
  "PreviousTxnID": "F19AD4577212D3BEACA0F75FE1BA1644F2E854D46E8D62E9C95D18E9708CBFB1",
  "PreviousTxnLgrSeq": 4,
  "TicketSequence": 3
}
```

### Campos

Un objeto `Ticket` tiene los siguientes campos:

| Nombre                | Tipo JSON | Tipo Interno | ¿Requerido? | Descripción                                                                                                                                                                                                                                                               |
| ------------------- | --------- | ------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Account`           | String    | AccountID     | Yes       | La cuenta que posee este Ticket.                                                                                                                                                                                                                                        |
| `Flags`             | Number    | UInt32        | Yes       | Un mapa de bits de flags booleanos habilitados para este objeto. Actualmente, el protocolo no define flags para los objetos `Ticket`. El valor es siempre `0`.                                                                                                                               |
| `LedgerEntryType`   | String    | UInt16        | Yes       | El valor `0x0054`, mapeado a la cadena `Ticket`, indica que este objeto es un objeto \{{currentpage.name\}}.                                                                                                                                                         |
| `OwnerNode`         | String    | UInt64        | Yes       | Una pista que indica qué página del directorio del propietario enlaza a este objeto, en caso de que el directorio consista en varias páginas. **Nota:** El objeto no contiene un enlace directo al directorio del propietario, ya que ese valor se puede derivar de la `Account`. |
| `PreviousTxnID`     | String    | Hash256       | Yes       | El hash identificador de la transacción que más recientemente modificó este objeto.                                                                                                                                                                                          |
| `PreviousTxnLgrSeq` | Number    | UInt32        | Yes       | El \[índice del ledger]\[Ledger Index] que contiene la transacción que más recientemente modificó este objeto.                                                                                                                                                          |
| `TicketSequence`    | Number    | UInt32        | Yes       | El \[número de secuencia]\[] que este Ticket reserva.                                                                                                                                                                                                                         |

### Formato de ID de Ticket

El ID de un objeto Ticket es el SHA-512Half de los siguientes valores, concatenados en orden:

* La clave de espacio del Ticket (`0x0054`)
* El AccountID del propietario del Ticket
* El número `TicketSequence` del Ticket
