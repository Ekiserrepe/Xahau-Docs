---
description: >-
  Una transacción AccountDelete elimina una cuenta y cualquier objeto que posea en Xahau, si es posible, enviando el XAH restante de la cuenta a una cuenta de destino especificada.
---

# AccountDelete

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/DeleteAccount.cpp)]

_Añadido por la enmienda DeletableAccounts_

{% hint style="danger" %}
 La enmienda _DeletableAccounts_ no está habilitada en este momento.
{% endhint %}

### Ejemplo

```json
{
    "TransactionType": "AccountDelete",
    "Account": "rWYkbWkCeg8dP6rXALnjgZSjjLyih5NXm",
    "Destination": "rPT1Sjq2YGrBMTttX4GZHjKu9dyfzbpAYe",
    "DestinationTag": 13,
    "Fee": "2000000",
    "Sequence": 2470665,
    "Flags": 2147483648
}
```

| Campo            | Tipo JSON              | \[Tipo Interno]\[] | Descripción                                                                                                                                                            |
| ---------------- | ---------------------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Destination`    | String - \[Dirección]\[] | AccountID           | La dirección de una cuenta que recibirá cualquier XAH restante después de eliminar la cuenta de envío. Debe ser una cuenta financiada en el ledger y no debe ser la cuenta de envío. |
| `DestinationTag` | Number                 | UInt32              | _(Opcional)_ Etiqueta de destino arbitraria que identifica a un destinatario alojado u otra información para el destinatario del XAH restante de la cuenta eliminada.                |

### Coste Especial de Transacción

Como un elemento disuasorio adicional contra el spam en el ledger, la transacción AccountDelete requiere un coste de transacción mucho más alto de lo habitual: en lugar del mínimo estándar de 0.00001 XAH, AccountDelete debe destruir al menos la cantidad de la reserva del propietario, actualmente 2 XRP. Esto desincentiva la creación excesiva de nuevas cuentas porque el requisito de reserva no se puede recuperar completamente al eliminar la cuenta.

El coste de la transacción siempre se aplica cuando una transacción se incluye en un ledger validado, incluso si la transacción no logra eliminar la cuenta. (Ver Casos de Error). Para reducir en gran medida las posibilidades de pagar el alto coste de la transacción si la cuenta no se puede eliminar, envía la transacción con `fail_hard` habilitado.

### Casos de Error

Además de los errores que pueden ocurrir en todas las transacciones, las transacciones AccountDelete pueden resultar en los siguientes códigos de resultado de transacción:

| Codigo de Error           | Descripción                                                                                                                                                                                                                                                                                                                         |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `temDISABLED`        | Ocurre si la enmienda DeletableAccounts no está habilitada.                                                                                                                                                                                                                                                                           |
| `temDST_IS_SRC`      | Ocurre si el `Destination` coincide con el remitente de la transacción (campo `Account`).                                                                                                                                                                                                                                                |
| `tecDST_TAG_NEEDED`  | Ocurre si la cuenta `Destination` requiere una etiqueta de destino, pero no se proporcionó el campo `DestinationTag`.                                                                                                                                                                                                                    |
| `tecNO_DST`          | Ocurre si la cuenta `Destination` no es una cuenta financiada en el ledger.                                                                                                                                                                                                                                                          |
| `tecNO_PERMISSION`   | Ocurre si la cuenta `Destination` requiere autorización de depósito y el remitente no está preautorizado.                                                                                                                                                                                                                             |
| `tecTOO_SOON`        | Ocurre si el número de `Sequence` del remitente es demasiado alto. El número de `Sequence` de la transacción más 256 debe ser menor que el \[Ledger Index]\[] actual. Esto previene la repetición de transacciones antiguas si esta cuenta es resucitada después de ser eliminada.                                                                                   |
| `tecHAS_OBLIGATIONS` | Ocurre si la cuenta a eliminar está conectada a objetos que no pueden ser eliminados en el ledger. (Esto incluye objetos creados por otras cuentas, como escrows y, por ejemplo, NFT's minteados, [incluso si son propiedad de otra cuenta](https://github.com/XRPLF/rippled/blob/develop/src/ripple/app/tx/impl/DeleteAccount.cpp#L197).) |
| `tefTOO_BIG`         | Ocurre si la cuenta remitente está vinculada a más de 1000 objetos en el ledger. La transacción podría tener éxito en un reintento si algunos de esos objetos se eliminan por separado primero.                                                                                                                                                     |

