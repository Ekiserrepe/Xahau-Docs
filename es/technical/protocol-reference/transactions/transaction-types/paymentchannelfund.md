---
description: >-
  Agrega una cantidad adicional a un canal de pago abierto y, opcionalmente, actualiza el tiempo de expiración del canal. Solo la dirección de origen del canal puede usar esta transacción.
---

# PaymentChannelFund

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/app/tx/impl/PayChan.cpp)

_Añadido por la \[enmienda PayChan]\[]._

### Ejemplo

```json
{
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "TransactionType": "PaymentChannelFund",
    "Channel": "C1AE6DDDEEC05CF2978C0BAD6FE302948E9533691DC749DCDD3B9E5992CA6198",
    "Amount" : {
         "currency" : "USD",
         "value" : "1",
         "issuer" : "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn"
      },
    "Expiration": 543171558
}
```

| Campo        | Tipo JSON                                                                                                                          | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Channel`    | String                                                                                                                             | Hash256             | El ID único del canal a financiar, como un string hexadecimal de 64 caracteres.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `Amount`     | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | Cantidad a agregar al canal. Debe ser una cantidad positiva.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       |
| `Expiration` | Number                                                                                                                             | UInt32              | _(Opcional)_ Nuevo tiempo de `Expiration` a establecer para el canal, en \[segundos desde la Ripple Epoch]\[]. Esto debe ser más tarde que el tiempo actual más el `SettleDelay` del canal, o la `Expiration` existente del canal. Después del tiempo de `Expiration`, cualquier transacción que acceda al canal cierra el canal sin realizar su acción normal. Cualquier cantidad no gastada se devuelve a la dirección de origen cuando el canal se cierra. (`Expiration` es independiente del tiempo inmutable `CancelAfter` del canal). Para más información, consulta el tipo de objeto de ledger PayChannel. |

### Casos de error

Además de los errores que pueden ocurrir para todas las transacciones, las transacciones \{{currentpage.name\}} pueden resultar en los siguientes códigos de resultado de la transacción:

| Código de error                | Descripción                                                                                                                                                                                        |
| ------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tecINSUFFICIENT_RESERVE` | La cuenta remitente tiene menos XAH que el requisito de reserva.                                                                                                                                     |
| `tecNO_DST`               | La cuenta de destino del canal ha sido eliminada. Esto solo es posible si el canal de pago se creó antes de que la enmienda fixPayChanRecipientOwnerDir se habilitara (el 01-05-2020). |
| `tecNO_ENTRY`             | El canal de pago identificado por el campo `Channel` no existe.                                                                                                                              |
| `tecNO_PERMISSION`        | El remitente de la transacción no es la dirección de origen del canal.                                                                                                                           |
| `tecUNFUNDED`             | La cuenta remitente no tiene suficiente cantidad para financiar el canal con la cantidad solicitada y aún cumplir con el requisito de reserva.                                                              |
| `temBAD_AMOUNT`           | El campo `Amount` de la transacción es inválido. La cantidad no puede ser cero o negativa.                                                                                                           |
| `temBAD_EXPIRATION`       | El campo `Expiration` es inválido.                                                                                                                                                                 |
