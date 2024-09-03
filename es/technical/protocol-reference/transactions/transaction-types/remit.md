---
description: >-
  La transacción Remit permite al usuario enviar múltiples tipos de pago, acuñar un URIToken, transferir una lista de URITokens y activar una cuenta.
---

# Remit

{% hint style="warning" %}
La transacción Remit paga todas las tarifas para _Activación de Cuenta, Líneas de Confianza y Reservas de URIToken._
{% endhint %}

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/Remit.cpp)]

_(Añadido por la \[enmienda Remit]\[].)_

### Ejemplo

```json
{
    "TransactionType": "Remit",
    "Account": "rGvbdrdCxG2tk9ZU2673XmsjRdHCDQEpt7",
    "Amounts": [
        {
            "AmountEntry": {
                "Amount": "1000000"
            }
        }, {
            "AmountEntry": {
                "Amount": {
                    "currency": "USD",
                    "issuer": "rExKpRKXNz25UAjbckCRtQsJFcSfjL9Er3",
                    "value": "1"
                }
            }
        }
    ],
    "Destination": "rG1QQv2nh2gr7RCZ1P8YYcBUKCCN633jCn",
    "URITokenIDs": [
        "714F206C865D334721B2F3388BEAF33AA91BC1D78C71941D10A2A653C873EDD3"
    ],
    "MintURIToken": {
        "Digest": "6F11A4DF4EE794E2800BB361173D454BFBECB3D7506C4F4CB0EC5AE98BE43747",
        "Flags": 1,
        "URI": "697066733A2F2F"
    }
}
```

| Campo            | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                      |
| ---------------- | --------- | ------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `Account`        | String    | AccountID           | La dirección de la cuenta que activará la cuenta, enviará el pago y/o acuñará/transferirá los URITokens. |
| `Destination`    | String    | AccountID           | La dirección única de la cuenta que recibirá el pago y/o los URITokens.
                                       |
| `DestinationTag` | Number    | UInt32              | _(Opcional)_ Un DestinationTag para depósitos en una cuenta de custodia compartida.                                          |
| `MintURIToken`   | Object    | STObject            | _(Opcional)_ Un STObject `MintURIToken` que contiene los detalles del URIToken que deseas acuñar en la `Destination`.    |
| `URITokenIDs`    | Array     | STArray             | _(Opcional)_ Una array de URITokenIDs (hash de 256 bits) para ser transferidos a la `Destination`. _Límite 32._       |
| `Amounts`        | Array     | STArray             | _(Opcional)_ Una array de STObjects AmountEntry que la cuenta desea enviar a la `Destination`. _Límite 32._     |
| `Inform`         | String    | AccountID           | _(Opcional)_ Una dirección única de una cuenta que puede tener un hook instalado y ser informada cuando ocurra un remit.  |
| `Blob`           | String    | Blob                | _(Opcional)_ Valor hexadecimal arbitrario que puede añadirse a la transacción para su uso en Hooks.                                   |
| `InvoiceID`      | String    | Hash256             | _(Opcional)_ Hash arbitrario de 256 bits que representa un motivo o identificador específico para este remit.                 |

### Campos AmountEntry

| Campo    | Tipo JSON                                                                                                                          | \[Internal Type]\[] | Descripción                                                            |
| -------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ---------------------------------------------------------------------- |
| `Amount` | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | La cantidad de moneda que la cuenta desea enviar a la `Destination`. |

### Campos MintURIToken

| Campo    | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                 |
| -------- | --------- | ------------------- | ----------------------------------------------------------- |
| `URI`    | String    | String              | La URI asociada con el URIToken acuñado. (256 bytes máx)
 |
| `Digest` | String    | Hash256             | _(Opcional)_ El digest del URIToken.                    |
| `Flags`  | Number    | UInt32              | _(Opcional) Flags en la transacción de acuñación.                  |

### Flags MintURIToken

El STObject `MintURIToken` admite los valores en el campo `Flags`, de la siguiente manera:

| Nombre Flag    | Valor Hex    | Valor Decimal | Descripción                                                                                     |
| ------------ | ------------ | ------------- | ----------------------------------------------------------------------------------------------- |
| `tfBurnable` | `0x00000001` | 1             | Permite al emisor destruir el `URIToken` acuñado. (El propietario del `URIToken` _siempre_ puede hacerlo.) |

### Coste Especial de la Transacción

La transacción Remit tiene un coste de transacción estándar, que es la tarifa mínima requerida para cualquier transacción en Xahau.

Además de la tarifa mínima requerida, la transacción Remit también deducirá las tarifas para lo siguiente:

| Acción               | Comisión                          |
| -------------------- | ---------------------------- |
| `Account Activation` | Requisito de reserva standard |
| `Create Trustline`   | Requisito de reserva standard |
| `URIToken Mint`      | Requisito de reserva standard |
| `URIToken Transfer`  | Requisito de reserva standard |

### Casos de errores

Además de los errores que pueden ocurrir en todas las transacciones, las transacciones Remit pueden resultar en los siguientes códigos de resultado de transacción:

| Código de error                     | Descripción                                                                                                                                        |
| ------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `temINVALID_FLAG`              | Ocurre si se especifica cualquier flag específico `tfFullyCanonicalSig`.                                                                                               |
| `temREDUNDANT`                 | Ocurre si la Cuenta es la misma que el Destino.                                                                                               |
| `temMALFORMED (Inform & Blob)` | Ocurre cuando: sfInform es igual a " "fuente o destino. Blob era mayor de 128kib.                                                              |
| `temMALFORMED (AmountEntry)`   | Ocurre cuando: la cuenta de AmountEntry excede `32`. Se esperaba AmountEntry. La Moneda Nativa aparece más de una vez. La Moneda Emitida aparece más de una vez. |
| `temMALFORMED (MintURIToken)`  | Ocurre cuando: sfMintURIToken contiene un campo inválido. URI no fue proporcionada. URI era demasiado larga/corta. UTF8 inválido dentro de MintURIToken.                |
| `temMALFORMED (URITokenIDs)`   | Ocurre cuando: URITokenIDs son demasiado cortos/largos. URITokenID duplicado.                                                                                     |
| `temBAD_AMOUNT`                | Ocurre cuando una Cantidad en AmountEntry es inválida.                                                                                               |
| `terNO_ACCOUNT`                | Ocurre cuando la cuenta fuente no existe.                                                                                                     |
| `tecNO_TARGET`                 | Ocurre cuando el campo `sfInform` está presente pero la cuenta no existe.                                                                        |
| `tecNO_PERMISSION`             | Ocurre cuando `disallowIncomingRemit` está habilitado en el `Destination`.                                                                                |
| `tecNO_PERMISSION`             | Ocurre cuando el `Destination` tiene habilitada la `DepositAuthorization`.                                                                                  |
| `tecDST_TAG_NEEDED`            | Ocurre si la cuenta de destino requiere una etiqueta de destino, pero la transacción no la incluye.                                            |
| `tecDUPLICATE`                 | Ocurre cuando la URI de `MintURIToken` de ese emisor ya existe.                                                                                |
| `tecDIR_FULL`                  | Ocurre cuando el directorio de las cuentas de origen o destino está lleno.                                                                                   |
| `tecNO_ENTRY`                  | Ocurre cuando el URIToken no existe.                                                                                                           |
| `tecNO_PERMISSION`             | Ocurre cuando el URIToken no es propiedad de la cuenta de origen.                                                                                       |
| `tecUNFUNDED_PAYMENT`          | Ocurre cuando la cuenta de origen no tiene los fondos necesarios para ejecutar la transacción. (XAH o Divisas Emitidas)                             |
