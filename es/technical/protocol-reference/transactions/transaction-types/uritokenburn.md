---
description: >-
  La transacción URITokenBurn se utiliza para quemar un URIToken en Xahau. Quemar un URIToken lo elimina permanentemente de la circulación.
---

# URITokenBurn

[\[Fuente\]](https://github.com/ripple/rippled/blob/develop/src/ripple/app/tx/impl/URIToken.cpp)

_(Añadido por la \[enmienda URIToken]\[].)_

La transacción URITokenBurn se utiliza para quemar un URIToken en Xahau. Quemar un URIToken lo retira permanentemente de la circulación.

### Ejemplo

```json
{
    "TransactionType": "URITokenBurn",
    "Account": "rWYkbWkCeg8dP6rXALnjgZSjjLyih5NXm",
    "URITokenID": "C1AE6DDDEEC05CF2978C0BAD6FE27362498DGS691DC749DCDD3B95992978C0BA",
}
```

| Campo        | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                     |
| ------------ | --------- | ------------------- | --------------------------------------------------------------- |
| `Account`    | String    | AccountID           | La dirección de la cuenta que posee el URIToken que se va a quemar. |
| `URITokenID` | String    | Hash256             | El ID del URIToken que se va a quemar.                                 |

### Coste Especial de la Transacción

La transacción URITokenBurn no tiene requisitos especiales de costo de transacción.

### Casos de Error

Además de los errores que pueden ocurrir en todas las transacciones, las transacciones URITokenBurn pueden resultar en los siguientes códigos de resultado:

| Código de Error         | Descripción                                                       |
| ------------------ | ----------------------------------------------------------------- |
| `tecNO_PERMISSION` | Ocurre si la cuenta no tiene permiso para quemar el token. |

