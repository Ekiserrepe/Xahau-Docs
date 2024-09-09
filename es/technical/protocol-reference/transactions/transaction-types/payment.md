---
description: >-
   Una transacción de tipo Payment representa una transferencia de valor de una cuenta a otra. (Dependiendo del camino tomado, esto puede involucrar intercambios adicionales de valor, los cuales ocurren de manera atómica).
---

# Payment

[\[Fuente\]](https://github.com/ripple/rippled/blob/5425a90f160711e46b2c1f1c93d68e5941e4bfb6/src/ripple/app/transactors/Payment.cpp)

### Ejemplo

```json
{
  "TransactionType" : "Payment",
  "Account" : "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
  "Destination" : "ra5nK24KXen9AHvsdFTKHSANinZseWnPcX",
  "Amount" : {
     "currency" : "USD",
     "value" : "1",
     "issuer" : "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn"
  },
  "Fee": "12",
  "Flags": 2147483648,
  "Sequence": 2,
}
```

| Campo            | Tipo JSON                                                                                                                          | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| ---------------- | ---------------------------------------------------------------------------------------------------------------------------------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `Amount`         | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | La cantidad de moneda a entregar. Para cantidades que no sean XAH, los nombres de los campos anidados DEBEN estar en minúsculas. Si el flag `tfPartialPayment` está activado, se entregará _hasta_ esta cantidad en su lugar.                                                                                                                                                                                                                                                                   |
| `Destination`    | String                                                                                                                             | AccountID           | La dirección única de la cuenta que recibe el pago.                                                                                                                                                                                                                                                                                                                                                                                         |
| `DestinationTag` | Number                                                                                                                             | UInt32              | _(Opcional)_ Tag arbitrario que identifica el motivo del pago al destino o un destinatario hospedado para pagar.                                                                                                                                                                                                                                                                                                                          |
| `InvoiceID`      | String                                                                                                                             | Hash256             | _(Opcional)_ Hash arbitrario de 256 bits que representa un motivo o identificador específico para este pago.                                                                                                                                                                                                                                                                                                                                               |
| `Paths`          | Array of path arrays                                                                                                               | PathSet             | (Opcional, auto-completable) Array de rutas de pago a ser utilizadas en esta transacción. Debe omitirse para transacciones XAH-a-XAH.                                                                                                                                                                                                                                                                                                                   |
| `SendMax`        | [Currency Amount](https://docs.xahau.network/technical/protocol-reference/data-types/currency-formats#specifying-currency-amounts) | Amount              | _(Opcional)_ La cantidad máxima de la moneda fuente que esta transacción puede costar, incluyendo tarifas de transferencia, tipos de cambio y [slippage](http://en.wikipedia.org/wiki/Slippage\_\(finance\)). No incluye el XAH destruido como costo por enviar la transacción. Para cantidades que no sean XAH, los nombres de los campos anidados DEBEN estar en minúsculas. Debe proporcionarse para pagos entre diferentes monedas o emisores. Debe omitirse para pagos XAH-a-XAH. |
| `DeliverMin`     | \[Currency Amount]\[]                                                                                                              | Amount              | _(Opcional)_ La cantidad mínima de moneda destino que esta transacción debería entregar. Solo es válido si es un pago parcial. Para cantidades que no sean XAH, los nombres de los campos anidados están en minúsculas.                                                                                                                                                                                                                                                        |

### Tipos de pagos

El tipo de transacción Payment es una herramienta de propósito general que puede representar varios tipos diferentes de acciones abstractas. Puedes identificar el tipo de transacción en función de los campos de la transacción, como se describe en la tabla a continuación:

| Tipo de pago                 | `Amount`                        | `SendMax`                       | `Paths`          | `Address` = `Destination`? | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| ---------------------------- | ------------------------------- | ------------------------------- | ---------------- | -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Pago Directo XAH-a-XAH    | String (XAH)                    | Omitido                         | Omitido          | No                         | Transfiere XAH directamente de una cuenta a otra. Siempre entrega la cantidad exacta. No se aplica ninguna tarifa aparte del costo básico de la transacción.                                                                                                                                                                                                                                                                                                                                                                                           |
| Creación o redención de tokens | Objeto                          | Objeto (opcional)               | Opcional         | No                         | Aumenta o disminuye la cantidad de una moneda o activo que no sea XAH registrado en Xahau. Las tarifas de transferencia y congelaciones no se aplican cuando se envía y redime directamente.                                                                                                                                                                                                                                                                                                                                                                        |
| Pago entre divisas       | Objeto (no-XAH) / String (XAH) | Objeto (non-XAH) / String (XAH) | Normalmente requerido | No                         | Envía tokens de un titular a otro. El `Amount` o `SendMax` pueden ser XAH o tokens, pero no pueden ambos ser XAH. Estos pagos "ripplean" a través del emisor y pueden tomar caminos más largos a través de varios intermediarios si la transacción especifica un conjunto de rutas. Las tarifas de transferencia establecidas por los emisores se aplican a este tipo de transacción. Estas transacciones consumen ofertas en el intercambio descentralizado para conectar diferentes monedas, o posiblemente incluso entre monedas con el mismo código de moneda y diferentes emisores. |
| Pagos parciales              | Objeto (no-XAH) / String (XAH) | Objeto (non-XAH) / String (XAH) | Normalmente requerido | No                         | Envía _hasta_ una cantidad específica de cualquier divisa. Utiliza el flag `tfPartialPayment`. Puede incluir una cantidad `DeliverMin` que especifique el mínimo que la transacción debe entregar para ser exitosa; si la transacción no especifica `DeliverMin`, puede tener éxito entregando _cualquier cantidad positiva_.                                                                                                                                                                                                                                 |
| Conversión de divisas          | Objeto (no-XAH) / String (XAH) | Objeto (no-XAH) / String (XAH) | Requerido         | Sí                        | Consume ofertas en el exchange descentralizado para convertir una divisa en otra, posiblemente aprovechando oportunidades de [arbitraje](https://en.wikipedia.org/wiki/Arbitrage). El `Amount` y `SendMax` no pueden ambos ser XAH. También se llama un pago circular porque entrega dinero al remitente. La API de Datos rastrea este tipo de transacción como un "intercambio" y no como un "pago".                                                                                                                                                     |

### Valores especiales de issuer para SendMax y Amount

La mayoría de las veces, el campo `issuer` de un \[Currency Amount]\[] que no sea XAH indica el emisor de un token. Sin embargo, al describir pagos, existen reglas especiales para el campo `issuer` en los campos `Amount` y `SendMax` de un pago.

* Siempre hay un único balance entre dos direcciones para el mismo código de divisa. Esto significa que, a veces, el campo `issuer` de una cantidad en realidad se refiere a una contraparte, en lugar de la dirección que emitió el token.
* Cuando el campo `issuer` del campo `Amount` de destino coincide con la dirección de `Destination`, se trata como un caso especial que significa "cualquier emisor que el destinatario acepte". Esto incluye todas las direcciones a las que el destinatario tiene líneas de confianza con un límite positivo, así como tokens con el mismo código de moneda emitidos por el destinatario.
* Cuando el campo `issuer` del campo `SendMax` coincide con la dirección de la cuenta fuente, se trata como un caso especial que significa "cualquier emisor que la fuente pueda usar". Esto incluye la creación de nuevos tokens en líneas de confianza que otras cuentas han extendido a la cuenta fuente y el envío de tokens que la cuenta fuente posee de otros emisores.

### Creación de cuentas

El tipo de transacción Payment puede crear nuevas cuentas en Xahau enviando suficiente XAH a una dirección no financiada. Otras transacciones a direcciones no financiadas siempre fallan.

Para más información, consulta Cuentas (Accounts).

### Paths

Si está presente, el campo `Paths` debe contener un _conjunto de rutas_ - un array de arreglos de rutas. Cada ruta individual representa una forma en que el valor puede fluir del remitente al receptor a través de varias cuentas intermediarias y libros de órdenes. Una sola transacción puede utilizar múltiples rutas, por ejemplo, si la transacción intercambia moneda utilizando varios libros de órdenes diferentes para lograr la mejor tasa.

Debes omitir el campo `Paths` para pagos directos, incluyendo:

* Una transferencia XAH-a-XAH.
* Una transferencia directa en una línea de confianza que conecta al remitente y receptor.

Si se proporciona el campo `Paths` o rutas, el servidor decide en el momento del procesamiento de la transacción qué rutas usar, del conjunto proporcionado más una _path predeterminado_ (la forma más directa posible de conectar las cuentas especificadas). Esta decisión es determinista e intenta minimizar los costes, pero no se garantiza que sea perfecta.

El campo `Paths` no debe ser un array vacío, ni un array cuyos miembros sean todos arreglos vacíos.

Para más información, ver Paths.

### Flags de Pagos

Las transacciones del tipo Payment admiten valores adicionales en el campo `Flags`, como se indica a continuación:

| Nombre del Flag          | Valor Hex    | Valor Decimal | Descripción                                                                                                                                                                          |
| ------------------ | ------------ | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `tfNoDirectRipple` | `0x00010000` | 65536         | No uses la ruta predeterminada; solo usa las rutas incluidas en el campo `Paths`. Esto está destinado a forzar la transacción a tomar oportunidades de arbitraje. La mayoría de los clientes no necesitan esto. |
| `tfPartialPayment` | `0x00020000` | 131072        | Si la cantidad especificada `Amount` no puede ser enviada sin gastar más de `SendMax`, reduce la cantidad recibida en lugar de fallar por completo. Consulta Pagos Parciales para más detalles.        |
| `tfLimitQuality`   | `0x00040000` | 262144        | Solo toma rutas donde todas las conversiones tienen una relación entrada
que es igual o mejor que la relación de `Amount`:`SendMax`. Consulta Calidad Límite para más detalles.                    |

### Pagos parciales

Un pago parcial permite que un pago tenga éxito al reducir la cantidad recibida. Los pagos parciales son útiles para devolver pagos sin incurrir en costos adicionales para uno mismo. Sin embargo, los pagos parciales también pueden ser utilizados para explotar integraciones que asuman ingenuamente que el campo `Amount` de una transacción exitosa siempre describe la cantidad exacta entregada.

Un pago parcial es cualquier [transacción Payment][] con el flag `tfPartialPayment` habilitado. Un pago parcial puede tener éxito si entrega cualquier cantidad positiva mayor o igual a su campo `DeliverMin` (o cualquier cantidad positiva en absoluto si `DeliverMin` no está especificado) sin enviar más que el valor de `SendMax`.

El campo `delivered_amount` de los metadatos de un pago indica la cantidad de moneda realmente recibida por la cuenta de destino.

Para más información, consulta el artículo completo sobre Pagos Parciales.

### Calidad Límite

Xahau define la "calidad" de una conversión de divisa como la relación entre la cantidad numérica entrada y la cantidad numérica salida. Por ejemplo, si gastas $2 USD para recibir £1 GBP, entonces la "calidad" de esa conversión es 0.5.

El flag `tfLimitQuality` te permite establecer una calidad mínima de conversiones que estás dispuesto a aceptar. Este límite de calidad se define como el `Amount` de destino dividido por la cantidad `SendMax` (solo los valores numéricos, independientemente de la moneda). Cuando está configurado, el motor de procesamiento de pagos evita usar cualquier ruta cuya calidad (tasa de conversión) sea peor (numéricamente menor) que el límite de calidad.

Por sí solo, el flag `tfLimitQuality` reduce las situaciones en las que una transacción puede tener éxito. Específicamente, rechaza pagos donde alguna parte del pago utiliza una conversión desfavorable, incluso si la calidad _promedio_ general de las conversiones en el pago es igual o mejor que el límite de calidad. Si un pago es rechazado de esta manera, el resultado de la transacción es `tecPATH_DRY`.

Considera el siguiente ejemplo. Si estoy intentando enviarte 100 Yuanes Chinos (`Amount` = 100 CNY) por 20 dólares estadounidenses (`SendMax` = 20 USD) o menos, entonces el límite de calidad es `5`. Imagina que un comerciante está ofreciendo ¥95 por $15 (una relación de aproximadamente `6.3` CNY por USD), pero la siguiente mejor oferta en el mercado es ¥5 por $2 (una relación de `2.5` CNY por USD). Si tomara ambas ofertas para enviarte 100 CNY, entonces me costaría 17 USD, con una calidad promedio de aproximadamente `5.9`.

Sin el flag `tfLimitQuality` activado, esta transacción tendría éxito, porque los $17 que me cuesta están dentro de mi `SendMax` especificado. Sin embargo, con el flag `tfLimitQuality` habilitado, la transacción fallaría en su lugar, porque la ruta para tomar la segunda oferta tiene una calidad de `2.5`, que es peor que el límite de calidad de `5`.

El flag `tfLimitQuality` es más útil cuando se combina con pagos parciales. Cuando tanto `tfPartialPayment` como `tfLimitQuality` están configurados en una transacción, la transacción entrega tanto del `Amount` de destino como pueda, sin utilizar ninguna conversión que sea peor que el límite de calidad.

En el ejemplo anterior con una oferta de ¥95/$15 y una oferta de ¥5/$2, la situación es diferente si mi transacción tiene tanto `tfPartialPayment` como `tfLimitQuality` habilitados. Si mantenemos mi `SendMax` de 20 USD y un `Amount` de destino de 100 CNY, entonces el límite de calidad sigue siendo `5`. Sin embargo, debido a que estoy haciendo un pago parcial, la transacción envía tanto como pueda en lugar de fallar si no se puede enviar la cantidad completa de destino. Esto significa que mi transacción consume la oferta de ¥95/$15, cuya calidad es de aproximadamente `6.3`, pero rechaza la oferta de ¥5/$2 porque la calidad de esa oferta de `2.5` es peor que el límite de calidad de `5`. Al final, mi transacción solo entrega ¥95 en lugar de los ¥100 completos, pero evita desperdiciar dinero en tasas de cambio desfavorables.
