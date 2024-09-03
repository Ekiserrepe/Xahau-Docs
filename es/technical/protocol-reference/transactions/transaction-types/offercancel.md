---
description: Una transacción OfferCancel elimina un objeto Offer de Xahau.
---

# OfferCancel

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

### Cancelar con OfferSequence

```json
{
    "TransactionType": "OfferCancel",
    "Account": "ra5nK24KXen9AHvsdFTKHSANinZseWnPcX",
    "Fee": "12",
    "Flags": 0,
    "LastLedgerSequence": 7108629,
    "OfferSequence": 6,
    "Sequence": 7
}
```

### Cancelar con OfferID

```json
{
    "TransactionType": "OfferCancel",
    "Account": "ra5nK24KXen9AHvsdFTKHSANinZseWnPcX",
    "Fee": "12",
    "Flags": 0,
    "LastLedgerSequence": 7108629,
    "OfferID": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0",
    "Sequence": 7
}
```

### Campos

| Campo           | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                                               |
| --------------- | --------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `OfferSequence` | Number    | UInt32              | _(Opcional)_ El número de secuencia (o número de Ticket) de una transacción OfferCreate anterior. Si se especifica, cancela cualquier objeto de oferta en el libro mayor que haya sido creado por esa transacción. No se considera un error si la oferta especificada no existe. |
| `OfferID`       | String    | Hash256             | _(Opcional)_ El ID del objeto de oferta en el ledger que se desea cancelar, como una cadena hexadecimal de 64 caracteres.                                                                                                                                                           |

_Consejo:_ Para eliminar una oferta antigua y reemplazarla con una nueva, puedes usar una \[transacción OfferCreate]\[] con un parámetro `OfferSequence`, en lugar de usar OfferCancel y otra OfferCreate.

El método OfferCancel devuelve `tesSUCCESS` incluso si no encontró una oferta con el número de secuencia correspondiente.
