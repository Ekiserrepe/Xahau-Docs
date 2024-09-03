---
description: >-
  Una transacción URIToken Mint genera un nuevo URIToken y asigna la propiedad a la cuenta especificada. El URIToken representa un activo digital único que se puede utilizar en varias aplicaciones.
---

# URITokenMint

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

_(Añadido por la \[enmienda URIToken]\[].)_

### Ejemplo

```json
{
    "TransactionType": "URITokenMint",
    "Account": "rWYkbWkCeg8dP6rXALnjgZSjjLyih5NXm",
    "Flags": 1,
    "URI": "697066733A2F2F4445414442454546",
    "Digest": "697066733A2F2F4445414442454546697066733A2F2F44454144424545467878",
    "Destination": "rPT1Sjq2YGrBMTttX4GZHjKu9dyfzbpAYe",
    "Amount": {
      "issuer": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
      "currency": "USD",
      "value": "100",
    },
}
```



| Campo         | Tipo JSON                                                                                                                          | \[Tipo Interno]\[] | Descripción                                                                                    |
| ------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ---------------------------------------------------------------------------------------------- |
| `Account`     | String                                                                                                                             | AccountID           | La dirección de la cuenta que poseerá el URIToken generado.                                  |
| `URI`         | String                                                                                                                             | String              | El URI asociado con el URIToken generado. (Máximo 256 bytes)                                    |
| `Digest`      | String                                                                                                                             | Hash256             | _(Opcional)_ El digest del URIToken.                                                       |
| `Destination` | String                                                                                                                             | AccountID           | _(Opcional)_ La dirección de la cuenta que puede comprar el URIToken generado.                      |
| `Amount`      | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | _(Opcional)_ La cantidad de moneda que la cuenta desea recibir a cambio del URIToken. |

### Flags URITokenMint

Las transacciones del tipo URITokenMint soportan valores adicionales en el campo `Flags`, de la siguiente manera:

| Nombre Flag    | Valor Hex    | Valor Decimal | Descripción                                                                                                                             |
| ------------ | ------------ | ------------- | --------------------------------------------------------------------------------------------------------------------------------------- |
| `tfBurnable` | `0x00000001` | 1             | Permite que el emisor (o una entidad autorizada por el emisor) destruya el `URIToken` generado. (El propietario del `URIToken` _siempre_ puede hacerlo). |

### Casos de error

### Coste Especial de Transacción

La transacción URIToken Mint tiene un costo de transacción estándar, que es el costo mínimo requerido para todas las transacciones.

### Casos de error

Además de los errores que pueden ocurrir en todas las transacciones, las transacciones URIToken Mint pueden resultar en los siguientes códigos de resultado:

| Código de error        | Descripción                                                                      |
| ----------------- | -------------------------------------------------------------------------------- |
| `tecDUPLICATE`    | Ocurre si ya existe un URIToken con el mismo URI del mismo emisor.  |
| `tecDIR_FULL`     | Ocurre si el directorio del propietario está lleno y no puede acomodar el nuevo URIToken. |
| `temINVALID_FLAG` | Ocurre cuando el usuario especifica un `Flag` incorrecto.                              |
| `terNO_ACCOUNT`   | Ocurre si la cuenta de envío no existe.                                    |
