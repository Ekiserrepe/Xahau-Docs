---
description: Secuestra XAH o IOU hasta que el proceso de depósito en garantía finalice o sea cancelado.
---

# EscrowCreate

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_Añadido por la \[enmienda Escrow]\[]._

### Ejemplo

```json
{
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "TransactionType": "EscrowCreate",
    "Amount" : {
       "currency" : "USD",
       "value" : "1",
       "issuer" : "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn"
    },
    "Destination": "rsA2LpzuawewSBQXkiju3YQTMzW13pAAdW",
    "CancelAfter": 533257958,
    "FinishAfter": 533171558,
    "Condition": "A0258020E3B0C44298FC1C149AFBF4C8996FB92427AE41E4649B934CA495991B7852B855810100",
    "DestinationTag": 23480,
    "SourceTag": 11747
}
```

### Campos

| Campo            | Tipo JSON                                                                                                                          | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                            |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Amount`         | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | Cantidad a deducir del saldo del remitente y poner en depósito. Una vez en depósito, la cantidad puede ir a la dirección `Destination` (después del tiempo `FinishAfter`) o devolverse al remitente (después del tiempo `CancelAfter`).                   |
| `Destination`    | String                                                                                                                             | AccountID           | Dirección que recibirá la cantidad en depósito.                                                                                                                                                                                                    |
| `CancelAfter`    | Number                                                                                                                             | UInt32              | _(Opcional)_ El tiempo, en \[segundos desde la Ripple Epoch]\[], en que expira este depósito. Este valor es inmutable; los fondos solo pueden devolverse al remitente después de este tiempo.                                                       |
| `FinishAfter`    | Number                                                                                                                             | UInt32              | _(Opcional)_ El tiempo, en \[segundos desde la Ripple Epoch]\[], en que la cantidad en depósito puede ser liberada al destinatario. Este valor es inmutable; los fondos no pueden moverse hasta que se alcance este tiempo.                                  |
| `Condition`      | String                                                                                                                             | Blob                | _(Opcional)_ Valor hexadecimal que representa una \[condición criptográfica PREIMAGE-SHA-256]\[](https://tools.ietf.org/html/draft-thomas-crypto-conditions-02#section-8.1). Los fondos solo pueden entregarse al destinatario si se cumple esta condición. |
| `DestinationTag` | Number                                                                                                                             | UInt32              | _(Opcional)_ Tag arbitrario para especificar aún más el destino de este pago en depósito, como un destinatario alojado en la dirección de destino.                                                                                        |

Se debe especificar `CancelAfter` o `FinishAfter`. Si se incluyen ambos, el tiempo de `FinishAfter` debe ser anterior al tiempo de `CancelAfter`.

Con la \[enmienda fix1571]\[] habilitada, debes proporcionar `FinishAfter`, `Condition` o ambos. \[Nuevo en: rippled 1.0.0]\[]

