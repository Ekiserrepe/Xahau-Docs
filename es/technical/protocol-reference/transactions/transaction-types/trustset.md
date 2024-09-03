---
description: Crear o modificar una línea de confianza que enlace dos cuentas.
---

# TrustSet

[\[Fuente\]](https://github.com/XRPLF/rippled/blob/master/src/ripple/app/tx/impl/SetTrust.cpp)

### Ejemplo

```json
{
    "TransactionType": "TrustSet",
    "Account": "ra5nK24KXen9AHvsdFTKHSANinZseWnPcX",
    "Fee": "12",
    "Flags": 262144,
    "LastLedgerSequence": 8007750,
    "LimitAmount": {
      "currency": "USD",
      "issuer": "rsP3mgGb2tcYUrxiLFiHJiQXhsziegtwBc",
      "value": "100"
    },
    "Sequence": 12
}
```

| Campo                    | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                   |
| ------------------------ | --------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `LimitAmount`            | Object    | Amount              | Objeto que define la línea de confianza a crear o modificar, en el formato de una \[Cantidad de Divisa]\[].                                                                                                 |
| `LimitAmount`.`currency` | String    | (Amount.currency)   | La divisa a la que se aplica esta línea de confianza, como un código de tres letras [Código de Moneda ISO 4217](https://www.xe.com/iso4217.php) o un valor hexadecimal de 160 bits según el formato de moneda. "XAH" es inválido. |
| `LimitAmount`.`value`    | String    | (Amount.value)      | Representación decimal entre comillas del límite a establecer en esta línea de confianza.                                                                                                                         |
| `LimitAmount`.`issuer`   | String    | (Amount.issuer)     | La dirección de la cuenta a la que se extiende la confianza.                                                                                                                                                |
| `QualityIn`              | Number    | UInt32              | _(Opcional)_ Valora los saldos entrantes en esta línea de confianza en la proporción de este número por 1,000,000,000 unidades. Un valor de `0` es un atajo para tratar los saldos al valor nominal.                 |
| `QualityOut`             | Number    | UInt32              | _(Opcional)_ Valora los saldos salientes en esta línea de confianza en la proporción de este número por 1,000,000,000 unidades. Un valor de `0` es un atajo para tratar los saldos al valor nominal.                 |

Si la cuenta especificada en `LimitAmount.issuer` está bloqueando líneas de confianza entrantes, la transacción falla con el código de resultado `tecNO_PERMISSION`. _(Requiere la enmienda \[DisallowIncoming]\[]: :no\_habilitada:)_.

### Flags TrustSet

Las transacciones del tipo TrustSet admiten valores adicionales en el campo `Flags`, como se indica a continuación:

| Nombre Flag         | Valor Hex    | Valor Decimal | Descripción                                                                                                                                        |
| ----------------- | ------------ | ------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tfSetfAuth`      | `0x00010000` | 65536         | Autoriza a la otra parte a poseer moneda emitida por esta cuenta. (Sin efecto a menos que se utilice el flag AccountSet `asfRequireAuth`). No se puede desactivar. |
| `tfSetNoRipple`   | `0x00020000` | 131072        | Habilita el flag No Ripple, que bloquea el rippling entre dos líneas de confianza de la misma moneda si este flag está habilitado en ambas.                     |
| `tfClearNoRipple` | `0x00040000` | 262144        | Desactiva el flag No Ripple, permitiendo el rippling en esta línea de confianza.                                                                                  |
| `tfSetFreeze`     | `0x00100000` | 1048576       | Congela la línea de confianza.                                                                                                                             |
| `tfClearFreeze`   | `0x00200000` | 2097152       | Descongela la línea de confianza.                                                                                                                           |

Si una transacción intenta habilitar No Ripple pero no puede, falla con el código de resultado `tecNO_PERMISSION`. Antes de que la \[enmienda fix1578]\[] se habilitara, una transacción de este tipo resultaba en `tesSUCCESS` (realizando cualquier otro cambio que pudiera) en su lugar.

El flag de Auth de una línea de confianza no determina si la línea de confianza cuenta para el requisito de reserva de XAH de su propietario. Sin embargo, un flag de Auth habilitado impide que la línea de confianza esté en su estado predeterminado. Una línea de confianza autorizada nunca puede ser eliminada. Un emisor puede preautorizar una línea de confianza solo con el flag `tfSetfAuth`, incluso si el límite y el saldo de la línea de confianza son 0.
