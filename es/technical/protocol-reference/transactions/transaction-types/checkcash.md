---
description: >-
  Intenta redimir un objeto Check en el ledger para recibir hasta la cantidad autorizada por la transacción CheckCreate correspondiente.
---

# CheckCash

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_(Añadido por la \[enmienda Checks]\[].)_

### Ejemplo

```json
{
    "Account": "rfkE1aSy9G8Upk4JssnwBxhEv5p4mn2KTy",
    "TransactionType": "CheckCash",
    "Amount": "100000000",
    "CheckID": "838766BA2B995C00744175F69A1B11E32C3DBC40E64801A4056FCBD657F57334",
    "Fee": "12"
}
```

| Campo        | Tipo JSON                                                                                                                          | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                     |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `CheckID`    | String                                                                                                                             | Hash256             | El ID del objeto Check en el ledger que se va a cobrar, como una cadena hexadecimal de 64 caracteres.                                                                                                                                                |
| `Amount`     | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | _(Opcional)_ Cobra el Check por exactamente esta cantidad, si es posible. La moneda debe coincidir con la de `SendMax` de la transacción CheckCreate correspondiente. Debes proporcionar este campo o `DeliverMin`.             |
| `DeliverMin` | \[Currency Amount]\[]                                                                                                              | Amount              | _(Opcional)_ Cobra el Check por al menos esta cantidad y por tanto como sea posible. La divisa debe coincidir con la de `SendMax` de la transacción CheckCreate correspondiente. Debes proporcionar este campo o `Amount`. |

La transacción _**debe**_ incluir `Amount` o `DeliverMin`, pero no ambos.

### Casos de error

* Si el remitente de la transacción CheckCash no es el `Destination` del cheque, la transacción falla con el código de resultado `tecNO_PERMISSION`.
* Si el Check identificado por el campo `CheckID` no existe, la transacción falla con el resultado `tecNO_ENTRY`.
* Si el Check identificado por el campo `CheckID` ya ha expirado, la transacción falla con el resultado `tecEXPIRED`.
* Si el destino del Check tiene habilitado el flag `RequireDest` pero el Check, tal como se creó, no tiene una etiqueta de destino, la transacción falla con el código de resultado `tecDST_TAG_NEEDED`.
* Si la transacción especifica tanto `Amount` como `DeliverMin`, o omite ambos, la transacción falla con el resultado `temMALFORMED`.
* Si `Amount` o `DeliverMin` no coincide con la divisa (y el emisor, si no es XAH) del Check, la transacción falla con el resultado `temBAD_CURRENCY`.
