---
description: >-
  La transacción URITokenBuy permite a un usuario comprar un URIToken del emisor. Esta transacción se utiliza para transferir la propiedad de un URIToken del emisor al comprador.
---

# URITokenBuy

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_(Añadido por la \[enmienda URIToken]\[].)_

### Ejemplo

```json
{
    "TransactionType": "URITokenBuy",
    "Account": "rWYkbWkCeg8dP6rXALnjgZSjjLyih5NXm",
    "URITokenID": "C1AE6DDDEEC05CF2978C0BAD6FE27362498DGS691DC749DCDD3B95992978C0BA",
    "Amount": {
      "issuer": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
      "currency": "USD",
      "value": "100",
    },
}
```

| Campo        | Tipo JSON                                                                                                                          | \[Tipo Interno]\[] | Descripción                                         |
| ------------ | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | --------------------------------------------------- |
| `Account`    | String                                                                                                                             | AccountID           | La dirección de la cuenta del comprador.                 |
| `URITokenID` | String                                                                                                                             | Hash256             | El identificador único del URIToken a comprar. |
| `Amount`     | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | La cantidad de moneda a pagar por el URIToken.     |

### Coste Especial de la Transacción

La transacción URITokenBuy tiene un costo estándar, que es la tarifa mínima requerida para cualquier transacción en Xahau.

### Casos de error

Además de los errores que pueden ocurrir en todas las transacciones, las transacciones URITokenBuy pueden resultar en los siguientes códigos de resultado:

| Código de error                         | Descripción                                                                                                                  |
| ---------------------------------- | ---------------------------------------------------------------------------------------------------------------------------- |
| `tecCANT_ACCEPT_OWN_NFTOKEN_OFFER` | Ocurre si el propietario del token es quien está reclamando la oferta.                                                              |
| `tecDST_TAG_NEEDED`                | Ocurre si la cuenta de destino requiere una etiqueta de destino, pero la transacción no incluye una.                      |
| `tecNO_PERMISSION`                 | Ocurre si el vendedor no tiene el token listado para la venta o si la `Destination` del token no es la cuenta compradora. |
| `temBAD_CURRENCY`                  | Ocurre cuando la moneda de compra no coincide con la moneda de la oferta.                                                           |
| `tecINSUFFICIENT_PAYMENT`          | Ocurre cuando la cantidad de compra es menor que la cantidad de la oferta.                                                                    |
| `tecINSUFFICIENT_FUNDS`            | Ocurre cuando el comprador no tiene fondos suficientes, incluyendo la `Fee`, para comprar el token.                               |
