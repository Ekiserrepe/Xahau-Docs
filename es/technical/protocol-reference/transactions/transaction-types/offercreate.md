---
description: >-
  Una transacción OfferCreate coloca una [Oferta](offers.html) en el [exchange descentralizado](decentralized-exchange.html).
---

# OfferCreate

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/URIToken.cpp)]

### Ejemplo

```json
{
    "TransactionType": "OfferCreate",
    "Account": "ra5nK24KXen9AHvsdFTKHSANinZseWnPcX",
    "Fee": "12",
    "Flags": 0,
    "LastLedgerSequence": 7108682,
    "Sequence": 8,
    "TakerGets": "6000000",
    "TakerPays": {
      "currency": "GKO",
      "issuer": "ruazs5h1qEsqpke88pcqnaseXdm6od2xc",
      "value": "2"
    }
}
```

### Campos

| Campo           | Tipo JSON                                                                                                                          | \[Tipo Interno]\[] | Descripción                                                                                           |
| --------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ----------------------------------------------------------------------------------------------------- |
| `Expiration`    | Number                                                                                                                             | UInt32              | _(Opcional)_ Tiempo después del cual la Oferta ya no estará activa, en \[segundos desde la Ripple Epoch]\[]. |
| `OfferSequence` | Number                                                                                                                             | UInt32              | _(Opcional)_ Una oferta para eliminar primero, especificada de la misma manera que \[OfferCancel]\[].                |
| `OfferID`       | String                                                                                                                             | Hash256             | _(Opcional)_ El ID del objeto de oferta en el libro mayor para cancelar, como una cadena hexadecimal de 64 caracteres.       |
| `TakerGets`     | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | La cantidad y tipo de moneda que se vende.                                                           |
| `TakerPays`     | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | La cantidad y tipo de moneda que se compra.                                                         |

### Flags de OfferCreate

Las transacciones del tipo OfferCreate admiten valores adicionales en el campo `Flags`, como se indica a continuación:

| Nombre del Flag             | Valor Hex    | Valor Decimal | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                 |
| --------------------- | ------------ | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tfPassive`           | `0x00010000` | 65536         | Si está habilitado, la Oferta no consume Ofertas que coincidan exactamente con ella y, en su lugar, se convierte en un objeto de Oferta en el libro mayor. Aún consume Ofertas que se crucen con ella.                                                                                                                                                                                                                                                             |
| `tfImmediateOrCancel` | `0x00020000` | 131072        | Trata la Oferta como una orden [Inmediata o Cancelar](http://en.wikipedia.org/wiki/Immediate\_or\_cancel). La Oferta nunca crea un \[objeto de Oferta]\[] en el ledger: solo negocia tanto como puede consumiendo Ofertas existentes en el momento en que se procesa la transacción. Si no hay Ofertas que coincidan, se ejecuta "exitosamente" sin negociar nada. En este caso, la transacción aún utiliza el código de resultado `tesSUCCESS`. |
| `tfFillOrKill`        | `0x00040000` | 262144        | Trata la oferta como una [orden Fill o Kill](http://en.wikipedia.org/wiki/Fill\_or\_kill). La Oferta nunca crea un [objeto de Oferta][] en el libro mayor y se cancela si no se puede llenar completamente en el momento de la ejecución. Por defecto, esto significa que el propietario debe recibir la cantidad completa de `TakerPays`; si el flag `tfSell` está habilitado, el propietario debe poder gastar la cantidad total de `TakerGets` en su lugar.         |
| `tfSell`              | `0x00080000` | 524288        | Intercambia la cantidad total de `TakerGets`, incluso si esto significa obtener más de la cantidad de `TakerPays` a cambio.                                                                                                                                                                                                                                                                                                            |

### Casos de error

| Código de error               | Descripción                                                                                                                                                                                                                                                                                                                                                 |
| ------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `temINVALID_FLAG`        | Ocurre si la transacción especifica tanto `tfImmediateOrCancel` como `tfFillOrKill`.                                                                                                                                                                                                                                                                          |
| `tecEXPIRED`             | Ocurre si la transacción especifica un tiempo de `Expiration` que ya ha pasado.                                                                                                                                                                                                                                                                           |
| `tecKILLED`              | Ocurre si la transacción especifica `tfFillOrKill` y no se puede llenar la cantidad completa. Si la _\[enmienda ImmediateOfferKilled]\[]_ está habilitada, este código de resultado también ocurre cuando la transacción especifica `tfImmediateOrCancel` y se ejecuta sin mover fondos (anteriormente, esto devolvía `tesSUCCESS`).                                       |
| `temBAD_EXPIRATION`      | Ocurre si la transacción contiene un campo `Expiration` que no está correctamente formateado.                                                                                                                                                                                                                                                                     |
| `temBAD_SEQUENCE`        | Ocurre si la transacción contiene un `OfferSequence` que no está correctamente formateado o es mayor que el número de `Sequence` de la propia transacción.                                                                                                                                                                                                             |
| `temBAD_OFFER`           | Ocurre si la Oferta intenta intercambiar XAH por XAH, o intenta intercambiar una cantidad inválida o negativa de un token.                                                                                                                                                                                                                                                 |
| `temREDUNDANT`           | Ocurre si la transacción especifica un token por el mismo token (mismo emisor y código de divisa).                                                                                                                                                                                                                                                             |
| `temBAD_CURRENCY`        | Ocurre si la transacción especifica un token con el código de moneda "XAH".                                                                                                                                                                                                                                                                                   |
| `temBAD_ISSUER`          | Ocurre si la transacción especifica un token con un valor `issuer` inválido.                                                                                                                                                                                                                                                                                 |
| `tecNO_ISSUER`           | Ocurre si la transacción especifica un token cuyo valor de `issuer` no es una cuenta financiada en el ledger.                                                                                                                                                                                                                                                     |
| `tecFROZEN`              | Ocurre si la transacción involucra un token en una línea de confianza congelada (incluyendo congelaciones locales y globales).                                                                                                                                                                                                                                                     |
| `tecUNFUNDED_OFFER`      | Ocurre si el propietario no tiene una cantidad positiva de la moneda `TakerGets`. (Excepción: si `TakerGets` especifica un token que emite el propietario, la transacción puede tener éxito.)                                                                                                                                                                          |
| `tecNO_LINE`             | Ocurre si la transacción involucra un token cuyo emisor utiliza Líneas de Confianza Autorizadas y no existe la línea de confianza necesaria.                                                                                                                                                                                                                            |
| `tecNO_AUTH`             | Ocurre si la transacción involucra un token cuyo emisor utiliza Líneas de Confianza Autorizadas y la línea de confianza que recibiría los tokens existe pero no ha sido autorizada.                                                                                                                                                                                |
| `tecINSUF_RESERVE_OFFER` | Ocurre si el propietario no tiene suficiente XAH para cumplir con el requisito de reserva de agregar un nuevo objeto de Oferta al ledger, y la transacción no convirtió ninguna divisa. (Si la transacción negoció con éxito alguna cantidad, la transacción tiene éxito con el código de resultado `tesSUCCESS`, pero no crea un objeto de Oferta en el ledger para el resto.) |
| `tecDIR_FULL`            | Ocurre si el propietario posee demasiados elementos en el libro mayor o si el libro de órdenes ya contiene demasiadas Ofertas al mismo tipo de cambio.                                                                                                                                                                                                                        |
