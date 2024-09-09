---
description: Una transacción AccountSet modifica las propiedades de una cuenta en Xahau.
---

# AccountSet

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/SetAccount.cpp)]

### Ejemplo

```json
{
    "TransactionType": "AccountSet",
    "Account" : "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "Fee": "12",
    "Sequence": 5,
    "Domain": "6578616D706C652E636F6D",
    "SetFlag": 5,
    "MessageKey": "03AB40A0490F9B7ED8DF29D246BF2D6269820A0EE7742ACDD457BEA7C7D0931EDB"
}
```

| Campo           | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                                                                               |
| --------------- | --------- | ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ClearFlag`     | Number    | UInt32              | _(Opcional)_ Identificador único de una bandera a desactivar para esta cuenta.                                                                                                                                                                                                                     |
| `Domain`        | String    | Blob                | _(Opcional)_ El dominio que posee esta cuenta, representado como una cadena hexadecimal que corresponde al ASCII en minúsculas del dominio. [No puede tener más de 256 bytes de longitud.](https://github.com/XRPLF/rippled/blob/55dc7a252e08a0b02cd5aa39e9b4777af3eafe77/src/ripple/app/tx/impl/SetAccount.h#L34) |
| `EmailHash`     | String    | Hash128             | _(Opcional)_ Un valor arbitrario de 128 bits. Convencionalmente, los clientes tratan esto como el hash md5 de una dirección de correo electrónico para usarla en la visualización de una imagen de [Gravatar](http://en.gravatar.com/site/implement/hash/).                                                                                    |
| `MessageKey`    | String    | Blob                | _(Opcional)_ Clave pública para enviar mensajes cifrados a esta cuenta. Para establecer la clave, debe tener exactamente 33 bytes, con el primer byte indicando el tipo de clave: `0x02` o `0x03` para claves secp256k1, `0xED` para claves Ed25519. Para eliminar la clave, usa un valor vacío.                    |
| `NFTokenMinter` | String    | Blob                | _(Opcional)_ Otra cuenta que puede acuñar NFTokens para ti. _(Añadido por la enmienda \[NonFungibleTokensV1\_1 amendment]\[].)_                                                                                                                                                                     |
| `SetFlag`       | Number    | UInt32              | _(Opcional)_ Flag en formato entero para habilitar en esta cuenta.                                                                                                                                                                                                                                     |
| `TransferRate`  | Number    | UInt32              | _(Opcional)_ La tarifa a cobrar cuando los usuarios transfieren los tokens de esta cuenta, representada en milmillonésimas de una unidad. No puede ser mayor que `2000000000` o menor que `1000000000`, excepto por el caso especial 0 que significa sin tarifa.                                                                |
| `TickSize`      | Number    | UInt8               | _(Opcional)_ Tamaño del tick a usar para ofertas que involucren una moneda emitida por esta dirección. Las tasas de cambio de esas ofertas se redondean a este número de dígitos significativos. Los valores válidos son `3` a `15` inclusive, o `0` para desactivar. _(Añadido por la enmienda [TickSize][])_                 |
| `WalletLocator` | String    | Hash256             | _(Opcional)_ Un valor arbitrario de 256 bits. Si se especifica, el valor se almacena como parte de la cuenta, pero no tiene ningún significado inherente ni requisitos.                                                                                                                                            |
| `WalletSize`    | Number    | UInt32              | _(Opcional)_ No se utiliza. Este campo es válido en transacciones AccountSet, pero no hace nada.                                                                                                                                                                                                   |

Si no se proporciona ninguna de estas opciones, la transacción AccountSet no tiene efecto (más allá de destruir el costo de la transacción). Consulta Cancelar o Saltar una Transacción para más detalles.

### Dominio

El campo `Domain` se representa como la cadena hexadecimal del ASCII en minúsculas del dominio. Por ejemplo, el dominio _example.com_ se representaría como `"6578616D706C652E636F6D"`.

Para eliminar el campo `Domain` de una cuenta, envía una transacción AccountSet con el campo Domain configurado en una cadena vacía.

Puedes poner cualquier dominio en el campo `Domain` de tu cuenta. Para demostrar que una cuenta y un dominio pertenecen a la misma persona o empresa, necesitas un "enlace bidireccional":

* Las cuentas que posees deben tener un dominio que posees en el campo `Domain`.
* En ese dominio, aloja un archivo xah-ledger.toml que enumere las cuentas que posees y, opcionalmente, otra información sobre cómo usas Xahau.

### Flags de AccountSet

Existen varias opciones que pueden habilitarse o deshabilitarse para una cuenta. Las opciones de cuenta se representan mediante diferentes tipos de flags según la situación:

* El tipo de transacción `AccountSet` tiene varios "Flags de AccountSet" (prefijadas con **`asf`**) que pueden habilitar una opción cuando se pasan como parámetro `SetFlag`, o deshabilitar una opción cuando se pasan como parámetro `ClearFlag`. Las opciones más recientes solo tienen este estilo de flag. Puedes habilitar hasta un flag `asf` por transacción y deshabilitar hasta un flag `asf` por transacción.
* El tipo de transacción `AccountSet` tiene varios flags de transacción (prefijadas con **`tf`**) que pueden usarse para habilitar o deshabilitar opciones específicas de cuenta cuando se pasan en el parámetro `Flags`. Puedes habilitar y deshabilitar una combinación de configuraciones en una transacción utilizando múltiples flags `tf`, pero no todas las configuraciones tienen flags `tf`.
* El tipo de objeto ledger `AccountRoot` tiene varios flags de estado de ledger (prefijadas con **`lsf`**) que representan el estado de opciones de cuenta particulares dentro de un ledger en particular. Estas configuraciones se aplican hasta que una transacción las cambie.

Para habilitar o deshabilitar los Flags de Cuenta, utiliza los parámetros `SetFlag` y `ClearFlag` de una transacción AccountSet. Las banderas AccountSet tienen nombres que comienzan con **`asf`**.

Todos los flags están deshabilitados por defecto.

Los flags disponibles de AccountSet son:


| Nombre del Flag                         | Valor Decimal | Flag correspondiente del Ledger         | Descripción                                                                                                                                                                                                                                                                                                                           |
| --------------------------------- | ------------- | --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `asfAccountTxnID`                 | 5             | (None)                            | Rastrear el ID de la transacción más reciente de esta cuenta. Requerido para `AccountTxnID`.                                                                                                                                                                                                                                                   |
| `asfAuthorizedNFTokenMinter`      | 10            | (None)                            | Habilita para permitir que otra cuenta acuñe tokens no fungibles (NFTokens) en nombre de esta cuenta. Especifica la cuenta autorizada en el campo `NFTokenMinter` del objeto AccountRoot. Para eliminar un minter autorizado, habilita esta bandera y omite el campo `NFTokenMinter`. _(Añadido por la enmienda \[NonFungibleTokensV1\_1]\[].)_ |
| `asfDefaultRipple`                | 8             | `lsfDefaultRipple`                | Habilita el rippling en las líneas de confianza de esta cuenta por defecto.                                                                                                                                                                                                                                                                             |
| `asfDepositAuth`                  | 9             | `lsfDepositAuth`                  | Habilita la Autorización de Depósito en esta cuenta. _(Añadido por la \[enmienda DepositAuth]\[].)_                                                                                                                                                                                                                                           |
| `asfDisableMaster`                | 4             | `lsfDisableMaster`                | Desactiva el uso del par de claves maestras. Solo puede habilitarse si la cuenta ha configurado otra forma de firmar transacciones, como una Clave Regular o una Lista de Firmantes.                                                                                                                                                                      |
| `asfDisallowIncomingCheck`        | 13            | `lsfDisallowIncomingCheck`        | Bloquea Cheques entrantes. _Añadido por la \[enmienda DisallowIncoming]\[]._                                                                                                                                                                                                                                                               |
| `asfDisallowIncomingNFTokenOffer` | 12            | `lsfDisallowIncomingNFTokenOffer` | Bloquea Ofertas de NFToken entrantes. _Añadido por la \[enmienda DisallowIncoming]\[]._                                                                                                                                                                                                                                                       |
| `asfDisallowIncomingPayChan`      | 14            | `lsfDisallowIncomingPayChan`      | Bloquea canales de pago de entrada. _Añadido por la \[enmienda DisallowIncoming]\[]._                                                                                                                                                                                                                                                     |
| `asfDisallowIncomingTrustline`    | 15            | `lsfDisallowIncomingTrustline`    | Bloquea las líneas de confianza entrantes._Añadido por la \[enmienda DisallowIncoming]\[]._                                                                                                                                                                                                                                                           |
| `asfDisallowIncomingRemit`        | 16            | `lsfDisallowIncomingRemit`        | Bloquea transacciones Remit entrantes._Añadido por la \[enmienda Remit]\[]._                                                                                                                                                                                                                                                                        |
| `asfDisallowXRP`                  | 3             | `lsfDisallowXRP`                  | No se debe enviar XAH a esta cuenta. (Consejo; no es obligatorio por el protocolo de Xahau).                                                                                                                                                                                                                                                   |
| `asfGlobalFreeze`                 | 7             | `lsfGlobalFreeze`                 | Congela todos los activos emitidos por esta cuenta.                                                                                                                                                                                                                                                                                             |
| `asfNoFreeze`                     | 6             | `lsfNoFreeze`                     | Renuncia permanentemente a la capacidad de congelar líneas de confianza individuales o desactivar el Congelamiento Global. Este flag nunca puede deshabilitarse después de ser habilitada.                                                                                                                                                                                       |
| `asfRequireAuth`                  | 2             | `lsfRequireAuth`                  | Requiere autorización para que los usuarios puedan tener balances emitidos por esta dirección. Solo puede habilitarse si la dirección no tiene líneas de confianza conectadas.                                                                                                                                                                                       |
| `asfRequireDest`                  | 1             | `lsfRequireDestTag`               | Requiere una etiqueta de destino para enviar transacciones a esta cuenta.                                                                                                                                                                                                                                                                       |
| `asfTshCollect`                   | 11            | `lsfTshCollect`                   | El TSH paga por la ejecución de su propia cadena de hooks. _(Añadido por la \[enmienda Hooks]\[])_._                                                                                                                                                                                                                                         |

Para habilitar los flags `asfDisableMaster` o `asfNoFreeze`, debes autorizar la transacción firmándola con el par de claves maestras. No puedes usar un par de claves regulares ni una firma múltiple. Puedes deshabilitar `asfDisableMaster` (es decir, reactivar el par de claves maestras) usando un par de claves regulares o una firma múltiple.

Los siguientes flags de transacción (`tf` flags), específicos del tipo de transacción AccountSet, cumplen la misma función. Debido al espacio limitado, algunas configuraciones no tienen banderas `tf` asociadas y no se están agregando nuevos flags `tf` al tipo de transacción `AccountSet`. Puedes usar una combinación de flags `tf` y `asf` para habilitar múltiples configuraciones con una sola transacción.

<table><thead><tr><th>Nombre del Flag</th><th>Valor Hex</th><th width="183">Valor Decimal</th><th>Reemplazado por el flag AccountSet</th></tr></thead><tbody><tr><td><code>tfRequireDestTag</code></td><td><code>0x00010000</code></td><td>65536</td><td><code>asfRequireDest</code> (<code>SetFlag</code>)</td></tr><tr><td><code>tfOptionalDestTag</code></td><td><code>0x00020000</code></td><td>131072</td><td><code>asfRequireDest</code> (<code>ClearFlag</code>)</td></tr><tr><td><code>tfRequireAuth</code></td><td><code>0x00040000</code></td><td>262144</td><td><code>asfRequireAuth</code> (<code>SetFlag</code>)</td></tr><tr><td><code>tfOptionalAuth</code></td><td><code>0x00080000</code></td><td>524288</td><td><code>asfRequireAuth</code> (<code>ClearFlag</code>)</td></tr><tr><td><code>tfDisallowXRP</code></td><td><code>0x00100000</code></td><td>1048576</td><td><code>asfDisallowXRP</code> (<code>SetFlag</code>)</td></tr><tr><td><code>tfAllowXRP</code></td><td><code>0x00200000</code></td><td>2097152</td><td><code>asfDisallowXRP</code> (<code>ClearFlag</code>)</td></tr></tbody></table>

**Precaución:** Los valores numéricos de los flags `tf` y `asf` en las transacciones no coinciden con los valores que establecen en las cuentas "en reposo" en el ledger. Para leer los flags de una cuenta en el ledger, consulta los flags de `AccountRoot`.

#### Bloquear Transacciones Entrantes

Las transacciones entrantes con propósitos poco claros pueden ser una inconveniencia para las instituciones financieras, que tendrían que reconocer cuando un cliente cometió un error y luego potencialmente reembolsar cuentas o ajustar balances dependiendo del error. Los flags `asfRequireDest` y `asfDisallowXRP` están diseñados para proteger a los usuarios de enviar fondos accidentalmente de manera que no quede claro el motivo del envío.

Por ejemplo, una etiqueta de destino se utiliza generalmente para identificar qué saldo alojado debe acreditarse cuando una institución financiera recibe un pago. Si se omite la etiqueta de destino, puede no quedar claro qué cuenta debe acreditarse, creando la necesidad de reembolsos, entre otros problemas. Al usar el flag `asfRequireDest`, puedes asegurarte de que cada pago entrante tenga una etiqueta de destino, lo que dificulta que otros te envíen un pago ambiguo por accidente.

Puedes protegerte contra pagos entrantes no deseados para monedas no-XAH al no crear líneas de confianza en esas monedas. Dado que XAH no requiere confianza, el flag `asfDisallowXRP` se utiliza para desalentar a los usuarios de enviar XAH a una cuenta. Sin embargo, esta bandera no es obligatoria en el protocolo de Xahau porque podría causar que las cuentas se vuelvan inoperables si se quedan sin XAH. En su lugar, las aplicaciones cliente deben deshabilitar o desalentar los pagos en XAH a cuentas con el flag `asfDisallowXRP` habilitado.

Si deseas bloquear _todos_ los pagos entrantes, puedes habilitar la Autorización de Depósitos. Esto evita que cualquier transacción te envíe dinero, incluso XAH, a menos que tu cuenta esté por debajo del requisito de reserva.

Si la \[enmienda DisallowIncoming]\[] :no está\activada:, también tienes la opción de bloquear todos los Cheques entrantes, Ofertas de NFToken, Canales de Pago y líneas de confianza. Generalmente es inofensivo estar en el extremo receptor de estos objetos, pero pueden impedir que elimines tu cuenta y puede ser confuso tener objetos que no esperabas mezclados con la lista de objetos que creaste. Para bloquear objetos entrantes, usa una o más de estos flags de cuenta:

* `asfDisallowIncomingCheck` - para objetos Check
* `asfDisallowIncomingNFTOffer` - para objetos NFTokenOffer
* `asfDisallowIncomingPayChan` - para objetos PayChannel
* `asfDisallowIncomingTrustline` - para objetos RippleState (trust line)

Cuando una transacción intentaría crear una de estas entradas en el ledger, si la cuenta de destino tiene el flag correspondiente habilitado, la transacción falla con el código de resultado `tecNO_PERMISSION`. A diferencia de la Autorización de Depósitos, estas configuraciones no evitan que recibas pagos en general. Además, habilitar esta configuración no te impide crear estos tipos de objetos tú mismo (a menos que el destino de tu transacción también esté usando la configuración, por supuesto).

### TransferRate

El campo `TransferRate` especifica una tarifa a cobrar siempre que las contrapartes transfieran la divisa que emites.

En las API HTTP y WebSocket, la tarifa de transferencia se representa como un entero, la cantidad que debe enviarse para que lleguen 1 mil millones de unidades. Por ejemplo, una tarifa de transferencia del 20% se representa como el valor `1200000000`. El valor no puede ser menor que `1000000000`. (Menos que eso indicaría regalar dinero por enviar transacciones, lo cual es explotable). Puedes especificar `0` como un atajo para `1000000000`, lo que significa sin tarifa.

Consulta Tarifas de Transferencia para obtener más información.
