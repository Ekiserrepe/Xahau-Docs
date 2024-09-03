---
description: Devuelve XAH o IOU en depósito en garantía al remitente.
---

# EscrowCancel

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_Añadido por la \[enmienda Escrow]\[]._

### Cancelación usando OfferSequence

```json
{
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "TransactionType": "EscrowCancel",
    "Owner": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "OfferSequence": 7,
}
```

### Cancelación usando EscrowID

```json
{
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "TransactionType": "EscrowCancel",
    "Owner": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "EscrowID": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0",
}
```

### Campos

| Campo           | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                               |
| --------------- | --------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| `Owner`         | String    | AccountID           | Dirección de la cuenta fuente que financió el pago en depósito en garantía.                                                             |
| `OfferSequence` | Number    | UInt32              | _(Opcional)_ Secuencia de la transacción (o número de Ticket) de la \[transacción EscrowCreate]\[] que creó el depósito en garantía a cancelar.
 |
| `EscrowID`      | String    | Hash256             | _(Opcional)_ El ID del objeto de ledger de Escrow a cancelar, como una cadena hexadecimal de 64 caracteres.                          |

Cualquier cuenta puede enviar una transacción EscrowCancel.

* Si la \[transacción EscrowCreate]\[] correspondiente no especificó un tiempo `CancelAfter`, la transacción EscrowCancel falla.
* De lo contrario, la transacción EscrowCancel falla si el tiempo `CancelAfter` es posterior al tiempo de cierre del ledger más recientemente cerrado.
