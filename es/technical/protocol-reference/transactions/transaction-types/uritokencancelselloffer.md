---
description: >-
  Una transacción URITokenCancelSellOffer cancela una oferta de venta para un URIToken en Xahau.
---

# URITokenCancelSellOffer

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_(Añadido por la \[enmienda URIToken]\[].)_

### Ejemplo

```json
{
    "TransactionType": "URITokenCancelSellOffer",
    "Account": "r9cZA1mLK5R5Am25ArfXFmqgNwjZgnfk59",
    "URITokenID": "C1AE6DDDEEC05CF2978C0BAD6FE27362498DGS691DC749DCDD3B95992978C0BA",
}
```



### Campos

| Campo        | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                    |
| ------------ | --------- | ------------------- | -------------------------------------------------------------- |
| `Account`    | String    | AccountID           | La dirección de la cuenta que posee la oferta de venta a cancelar.
 |
| `URITokenID` | String    | Hash256             | El ID del URIToken cuya oferta de venta se desea cancelar.               |

### Casos de error

Además de los errores que pueden ocurrir en todas las transacciones, las transacciones \{{currentpage.name\}} pueden resultar en los siguientes códigos de resultado:

| Codigo Error         | Descripción                                                             |
| ------------------ | ----------------------------------------------------------------------- |
| `tecNO_PERMISSION` | Ocurre cuando la cuenta que ejecuta la transacción no es la propietaria del token. |
