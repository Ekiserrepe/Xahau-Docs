---
description: >-
  Una transacción DepositPreauth otorga a otra cuenta la preaprobación para entregar pagos al remitente de esta transacción.
---

# DepositPreauth

**Consejo:** Puedes usar esta transacción para preautorizar a ciertas contrapartes antes de habilitar la Autorización de Depósitos. Esto puede ser útil para asegurar una transición sin problemas desde no requerir autorización de depósitos a requerirla.

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_Añadido por la \[enmienda DepositPreauth]\[]._

### Ejemplo

```json
{
  "TransactionType" : "DepositPreauth",
  "Account" : "rsUiUMpnrgxQp24dJYZDhmV4bE3aBtQyt8",
  "Authorize" : "rEhxGqkqPPSxQ3P25J66ft5TwpzV14k2de",
  "Fee" : "10",
  "Flags" : 2147483648,
  "Sequence" : 2
}
```

### Campos

| Campo         | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                      |
| ------------- | --------- | ------------------- | -------------------------------------------------------------------------------- |
| `Authorize`   | String    | AccountID           | _(Opcional)_ Dirección Xahau del remitente a preautorizar.                        |
| `Unauthorize` | String    | AccountID           | _(Opcional)_ Dirección Xahau de un remitente cuya preautorización debe ser revocada. |

Debes proporcionar _ya sea_ `Authorize` o `Unauthorize`, pero no ambos.

### Casos de error

* Una cuenta no puede preautorizar (o desautorizar) su propia dirección. Intentar hacerlo falla con el resultado `temCANNOT_PREAUTH_SELF`.
* Intentar preautorizar una cuenta que ya está preautorizada falla con el resultado `tecDUPLICATE`.
* Intentar desautorizar una cuenta que no está preautorizada falla con el resultado `tecNO_ENTRY`.
* Intentar preautorizar una dirección que no está financiada en el ledger falla con el resultado `tecNO_TARGET`.
* Agregar una autorización añade un objeto DepositPreauth al ledger, lo cual cuenta para el requisito de reserva del propietario. Si el remitente de la transacción no tiene suficiente XAH para pagar la reserva aumentada, la transacción falla con el resultado `tecINSUFFICIENT_RESERVE`. Si el remitente de la cuenta ya ha alcanzado el número máximo de objetos que posee, la transacción falla con el resultado `tecDIR_FULL`.
