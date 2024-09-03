---
description: >-
  Reclamar un monto de un canal de pago, ajustar la expiración del canal de pago o ambos. Esta transacción se puede utilizar de diferentes maneras dependiendo del rol del remitente en el canal especificado:
---

# PaymentChannelClaim

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/app/tx/impl/PayChan.cpp)

_Añadido por la \[enmienda PayChan]\[]._

La **dirección de origen** de un canal puede:

* Enviar una Amount (cantidad) desde el canal al destino con _o sin_ una Claim (reclamación) firmada.
* Establecer que el canal expire tan pronto como haya pasado el `SettleDelay` del canal.
* Borrar un tiempo de `Expiration` pendiente.
* Cerrar un canal inmediatamente, con o sin procesar una reclamación primero. La dirección de origen no puede cerrar el canal inmediatamente si queda un monto en el canal.

La **dirección de destino** de un canal puede:

* Recibir una cantidad del canal utilizando una Claim firmada.
* Cerrar el canal inmediatamente después de procesar una Claim, reembolsando cualquier cantidad no reclamada a la dirección de origen del canal.

**Cualquier dirección** que envíe esta transacción puede:

* Cerrar un canal si su tiempo de `Expiration` o `CancelAfter` es anterior al tiempo de cierre del ledger anterior. Cualquier transacción PaymentChannelClaim formada válidamente tiene este efecto independientemente del contenido de la transacción.

### Ejemplo

```json
{
  "Channel": "C1AE6DDDEEC05CF2978C0BAD6FE302948E9533691DC749DCDD3B9E5992CA6198",
  "Amount" : {
     "currency" : "USD",
     "value" : "1",
     "issuer" : "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn"
  },
  "Amount" : {
     "currency" : "USD",
     "value" : "1",
     "issuer" : "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn"
  },
  "Signature": "30440220718D264EF05CAED7C781FF6DE298DCAC68D002562C9BF3A07C1E721B420C0DAB02203A5A4779EF4D2CCC7BC3EF886676D803A9981B928D3B8ACA483B80ECA3CD7B9B",
  "PublicKey": "32D2471DB72B27E3310F355BB33E339BF26F8392D5A93D3BC0FC3B566612DA0F0A"
}
```

| Campo       | Tipo JSON                                                                                                                          | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Channel`   | String                                                                                                                             | Hash256             | El ID único del canal, como una string hexadecimal de 64 caracteres.                                                                                                                                                                                                                                                                                                                                                          |
| `Balance`   | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | _(Opcional)_ Cantidad total entregada por este canal después de procesar esta reclamación. Requerido para entregar un monto. Debe ser mayor que la cantidad total entregada por el canal hasta ahora, pero no mayor que la `Amount` de la reclamación firmada. Debe proporcionarse, excepto cuando se cierre el canal.                                                                                                                                    |
| `Amount`    | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | _(Opcional)_ La cantidad autorizada por la `Signature`. Esto debe coincidir con la cantidad en el mensaje firmado. Esta es la cantidad acumulada que se puede dispensar por el canal, incluyendo el XAH previamente redimido.                                                                                                                                                                                                    |
| `Signature` | String                                                                                                                             | Blob                | _(Opcional)_ La firma de esta reclamación, en hexadecimal. El mensaje firmado contiene el ID del canal y la cantidad de la reclamación. Requerido a menos que el remitente de la transacción sea la dirección de origen del canal.                                                                                                                                                                                                        |
| `PublicKey` | String                                                                                                                             | Blob                | _(Opcional)_ La clave pública utilizada para la firma, en hexadecimal. Esto debe coincidir con la `PublicKey` almacenada en el ledger para el canal. Requerido a menos que el remitente de la transacción sea la dirección de origen del canal y se omita el campo `Signature`. (La transacción incluye la clave pública para que `rippled` pueda verificar la validez de la firma antes de intentar aplicar la transacción al ledger.) |

Si el canal de pago fue creado antes de que la enmienda fixPayChanRecipientOwnerDir se habilitara (el 01-05-2020), es posible que el destino del canal de pago haya sido eliminado y no exista actualmente en el ledger. Si el destino ha sido eliminado, la cuenta de origen no puede enviar XAH desde el canal al destino; en su lugar, la transacción falla con `tecNO_DST`. (Y, por supuesto, la cuenta eliminada no puede enviar ninguna transacción en absoluto). Otros usos de este tipo de transacción no se ven afectados cuando la cuenta de destino ha sido eliminada, incluyendo ajustar la expiración del canal, cerrar un canal sin monto o eliminar un canal que ha pasado su tiempo de expiración.

### Flags de PaymentChannelClaim

Las transacciones del tipo PaymentChannelClaim admiten valores adicionales en el campo `Flags`, como se indica a continuación:

| Nombre del Flag | Valor Hex    | Valor Decimal | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
| --------- | ------------ | ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tfRenew` | `0x00010000` | 65536         | Borrar el tiempo de `Expiration` del canal. (`Expiration` es diferente del tiempo inmutable `CancelAfter` del canal). Solo la dirección de origen del canal de pago puede usar este flag.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `tfClose` | `0x00020000` | 131072        | Solicita cerrar el canal. Solo las direcciones de origen y destino del canal pueden usar este flag. Este flag cierra el canal inmediatamente si no queda cantidad asignada al canal después de procesar la reclamación actual, o si la dirección de destino lo usa. Si la dirección de origen usa este flag cuando el canal aún tiene un monto, esto programa el cierre del canal después de que hayan pasado los segundos especificados en `SettleDelay`. (Específicamente, esto establece la `Expiration` del canal en el tiempo de cierre del ledger anterior más el tiempo de `SettleDelay` del canal, a menos que el canal ya tenga un tiempo de `Expiration` anterior). Si la dirección de destino usa este flag cuando el canal aún tiene un monto, cualquier cantidad que quede después de procesar la reclamación se devuelve a la dirección de origen. |
