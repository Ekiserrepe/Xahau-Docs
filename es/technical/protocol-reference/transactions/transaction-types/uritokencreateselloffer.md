---
description: >-
  Una transacción URITokenCreateSellOffer permite a un usuario crear una oferta de venta para un URIToken en el intercambio descentralizado de Xahau.
---

# URITokenCreateSellOffer

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_(Añadido por la \[enmienda URIToken]\[].)_

### Ejemplo

```json
{
    "TransactionType": "URITokenCreateSellOffer",
    "Account": "rPT1Sjq2YGrBMTttX4GZHjKu9dyfzbpAYe",
    "URITokenID": "C1AE6DDDEEC05CF2978C0BAD6FE27362498DGS691DC749DCDD3B95992978C0BA",
    "Destination": "rPT1Sjq2YGrBMTttX4GZHjKu9dyfzbpAYe",
    "Amount": {
      "issuer": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
      "currency": "USD",
      "value": "100",
    },
}
```

### Campos

| Campo         | Tipo JSON                                                                                                                          | \[Tipo Interno]\[] | Descripción                                                                       |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | --------------------------------------------------------------------------------- |
| `Account`     | String                                                                                                                             | AccountID           | La dirección de la cuenta que crea la oferta de venta.                               |
| `URITokenID`  | String                                                                                                                             | Hash256             | El ID del URIToken que se está vendiendo.                                                |
| `Destination` | String                                                                                                                             | AccountID           | _(Opcional)_ La dirección de la cuenta que recibirá la oferta de venta.                |
| `Amount`      | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | La cantidad de moneda que la cuenta desea recibir a cambio del URIToken.
 |

### Casos de error

Además de los errores que pueden ocurrir en todas las transacciones, las transacciones URITokenCreateSellOffer pueden resultar en los siguientes códigos de resultado:

| Código de error               | Descripción                                                                      |
| ------------------------ | -------------------------------------------------------------------------------- |
| `tecNO_TARGET`           | Ocurre si la cuenta objetivo no existe.                                     |
| `tecNO_PERMISSION`       | Ocurre si la cuenta no tiene permiso para crear una oferta de venta.           |
| `tecINSUF_RESERVE_LINE`  | Ocurre si la cuenta no tiene un saldo de reserva suficiente para crear una oferta de venta. |
| `tecINSUF_RESERVE_OFFER` | Ocurre si la cuenta no tiene un saldo de reserva suficiente para crear una oferta de venta. |
| `tecUNFUNDED_OFFER`      | Ocurre si la cuenta no tiene un saldo suficiente para crear una oferta de venta. |
| `tecOFFER_INVALID`       | Ocurre si la oferta de venta es inválida.                                             |
