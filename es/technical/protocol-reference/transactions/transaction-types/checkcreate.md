---
description: >-
  Crea un objeto Check en el ledger, que es un pago diferido que puede ser cobrado por su destino previsto. El remitente de esta transacción es el emisor del Check.
---

# CheckCreate

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_(Añadido por la \[enmienda Checks]\[].)_

### Ejemplo

```json
{
  "TransactionType": "CheckCreate",
  "Account": "rUn84CUYbNjRoTQ6mSW7BVJPSVJNLb1QLo",
  "Destination": "rfkE1aSy9G8Upk4JssnwBxhEv5p4mn2KTy",
  "SendMax": "100000000",
  "Expiration": 570113521,
  "InvoiceID": "6F1DFD1D0FE8A32E40E1F2C05CF1C15545BAB56B617F9C6C2D63A6B704BEF59B",
  "DestinationTag": 1,
  "Fee": "12"
}
```

| Campo            | Tipo JSON                                                                                                                          | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                                                                                                 |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Destination`    | String                                                                                                                             | AccountID           | La dirección única de la cuenta que puede cobrar el Check.                                                                                                                                                                                                                                                  |
| `SendMax`        | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | Cantidad máxima de la moneda de origen que el Check puede debitar del remitente, incluidas las tarifas de transferencia en monedas que no sean XAH. El Check solo puede acreditar al destinatario con la misma moneda (del mismo emisor, para monedas que no sean XAH). Para cantidades que no sean XAH, los nombres de los campos anidados DEBEN estar en minúsculas.
 |
| `DestinationTag` | Number                                                                                                                             | UInt32              | _(Opcional)_ Etiqueta arbitraria que identifica el motivo del Check o un destinatario alojado al que pagar.                                                                                                                                                                                                          |
| `Expiration`     | Number                                                                                                                             | UInt32              | _(Opcional) Tiempo después del cual el Check ya no es válido, en \[segundos desde la Ripple Epoch]\[].                                                                                                                                                                                                        |
| `InvoiceID`      | String                                                                                                                             | Hash256             | _(Opcional)_ Hash arbitrario de 256 bits que representa un motivo o identificador específico para este Check.                                                                                                                                                                                                            |

### Casos de error

* Si la cuenta `Destination` está bloqueando Cheques entrantes, la transacción falla con el código de resultado `tecNO_PERMISSION`.&#x20;
* Si el `Destination` es el remitente de la transacción, la transacción falla con el código de resultado `temREDUNDANT`.
* Si la cuenta `Destination` no existe en el ledger, la transacción falla con el código de resultado `tecNO_DST`.
* Si la cuenta `Destination` tiene habilitada el flag `RequireDest` pero la transacción no incluye un campo `DestinationTag`, la transacción falla con el código de resultado `tecDST_TAG_NEEDED`.
* Si `SendMax` especifica un token que está congelado, la transacción falla con el resultado `tecFROZEN`.
* Si la `Expiration` de la transacción está en el pasado, la transacción falla con el resultado `tecEXPIRED`.
* Si el remitente no tiene suficiente XAH para cumplir con la reserva del propietario después de agregar el Check, la transacción falla con el resultado `tecINSUFFICIENT_RESERVE`.
* Si el remitente o el destinatario del Check no pueden poseer más objetos en el ledger, la transacción falla con el resultado `tecDIR_FULL`.
