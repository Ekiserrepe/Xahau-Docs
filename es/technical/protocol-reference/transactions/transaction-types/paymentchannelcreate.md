---
description: >-
  Crea un canal de pago y fúndelo con una cantidad específica. La dirección que envía esta transacción se convierte en la "dirección de origen" del canal de pago.
---

# PaymentChannelCreate

[\[Fuente\]](https://github.com/XRPLF/rippled/blob/master/src/ripple/app/tx/impl/PayChan.cpp)

_Añadido por la \[enmienda PayChan]\[]._

### Ejemplo

```json
{
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "TransactionType": "PaymentChannelCreate",
    "Amount" : {
       "currency" : "USD",
       "value" : "1",
       "issuer" : "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn"
    },
    "Destination": "rsA2LpzuawewSBQXkiju3YQTMzW13pAAdW",
    "SettleDelay": 86400,
    "PublicKey": "32D2471DB72B27E3310F355BB33E339BF26F8392D5A93D3BC0FC3B566612DA0F0A",
    "CancelAfter": 533171558,
    "DestinationTag": 23480,
    "SourceTag": 11747
}
```

| Campo            | Tipo JSON                                                                                                                          | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                                                                                                                   |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Amount`         | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | Cantidad a deducir del saldo del remitente y reservar en este canal. Mientras el canal esté abierto, la cantidad solo puede ir a la dirección `Destination`. Cuando el canal se cierra, cualquier cantidad no reclamada se devuelve al saldo de la dirección de origen.                                                                          |
| `Destination`    | String                                                                                                                             | AccountID           | Dirección que recibirá las reclamaciones contra este canal. También se conoce como la "dirección de destino" para el canal. No puede ser la misma que la del remitente (`Account`).                                                                                                                                                                |
| `SettleDelay`    | Number                                                                                                                             | UInt32              | Tiempo que la dirección de origen debe esperar antes de cerrar el canal si hay una cantidad no reclamada.                                                                                                                                                                                                                            |
| `PublicKey`      | String                                                                                                                             | Blob                | La clave pública de 33 bytes del par de claves que la fuente utilizará para firmar reclamaciones contra este canal, en formato hexadecimal. Esta puede ser cualquier clave pública secp256k1 o Ed25519. Para obtener más información sobre los pares de claves, consulte Key Derivation                                                                                                        |
| `CancelAfter`    | Number                                                                                                                             | UInt32              | _(Opcional)_ El tiempo, en \[segundos desde la Ripple Epoch]\[], en el que este canal expira. Cualquier transacción que modifique el canal después de este tiempo cierra el canal sin afectarlo de otra manera. Este valor es inmutable; el canal puede cerrarse antes de este tiempo, pero no puede permanecer abierto después de este tiempo. |
| `DestinationTag` | Number                                                                                                                             | UInt32              | _(Opcional)_ Etiqueta arbitraria para especificar aún más el destino de este canal de pago, como un destinatario alojado en la dirección de destino.                                                                                                                                                                                |

Si la cuenta `Destination` está bloqueando canales de pago entrantes, la transacción falla con el código de resultado `tecNO_PERMISSION`. (Requiere la \[enmienda DisallowIncoming]\[]
:no\_activada:)_
