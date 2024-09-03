---
description: >-
  Cancela un Cheque no canjeado, eliminándolo del ledger sin enviar dinero. Tanto el origen como el destino del cheque pueden cancelar un Cheque en cualquier momento utilizando este tipo de transacción.
  
  Cancels an unredeemed Check, removing it from the ledger without sending any
  money. The source or the destination of the check can cancel a Check at any
  time using this transaction type.
---

# CheckCancel

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_(Añadido por la \[enmienda Checks]\[].)_

### Ejemplo

```json
{
    "Account": "rUn84CUYbNjRoTQ6mSW7BVJPSVJNLb1QLo",
    "TransactionType": "CheckCancel",
    "CheckID": "49647F0D748DC3FE26BDACBC57F251AADEFFF391403EC9BF87C97F67E9977FB0",
    "Fee": "12"
}
```

| Campo     | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                        |
| --------- | --------- | ------------------- | ---------------------------------------------------------------------------------- |
| `CheckID` | String    | Hash256             | El ID del objeto Cheque en el ledger que se va a cancelar, como una cadena hexadecimal de 64 caracteres. |

### Casos de Error

* Si el objeto identificado por el `CheckID` no existe o no es un Cheque, la transacción falla con el resultado `tecNO_ENTRY`.
* Si el Cheque no ha expirado y el remitente de la transacción CheckCancel no es el origen o el destino del Cheque, la transacción falla con el resultado `tecNO_PERMISSION`.
