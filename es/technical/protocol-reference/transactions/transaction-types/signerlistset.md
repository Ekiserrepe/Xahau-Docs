---
description: >-
  La transacción SignerListSet crea, reemplaza o elimina una lista de firmantes que se pueden utilizar para firmar una transacción con múltiples firmas. Este tipo de transacción fue introducido por la [enmienda MultiSign][].
---

# SignerListSet

[\[Fuente\]](https://github.com/XRPLF/rippled/blob/master/src/ripple/app/tx/impl/SetSignerList.cpp)

### Ejemplo

```json
{
    "Flags": 0,
    "TransactionType": "SignerListSet",
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "Fee": "12",
    "SignerQuorum": 3,
    "SignerEntries": [
        {
            "SignerEntry": {
                "Account": "rsA2LpzuawewSBQXkiju3YQTMzW13pAAdW",
                "SignerWeight": 2
            }
        },
        {
            "SignerEntry": {
                "Account": "rUpy3eEg8rqjqfUoLeBnZkscbKbFsKXC3v",
                "SignerWeight": 1
            }
        },
        {
            "SignerEntry": {
                "Account": "raKEEVSGnKSD9Zyvxu4z6Pqpm4ABH8FS6n",
                "SignerWeight": 1
            }
        }
    ]
}
```

| Campo           | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                                                                                                                                                                     |
| --------------- | --------- | ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `SignerQuorum`  | Number    | UInt32              | Un número objetivo para los pesos de los firmantes. Una firma múltiple de esta lista es válida solo si la suma de los pesos de las firmas proporcionadas es mayor o igual a este valor. Para eliminar una lista de firmantes, utiliza el valor `0`.                                                                                                                                                        |
| `SignerEntries` | Array     | Array               | _(Omitido al eliminar)_ Array de objetos `SignerEntry`, que indican las direcciones y los pesos de los firmantes en esta lista. Esta lista de firmantes debe tener al menos 1 miembro y no más de 32 miembros. Ninguna dirección puede aparecer más de una vez en la lista, ni puede aparecer la `Account` que envía la transacción en la lista. _(Actualizado por la [enmienda ExpandedSignerList][])_. |

Una transacción SignerListSet exitosa reemplaza el objeto `SignerList` de la cuenta en el libro mayor, o agrega uno si no existía antes. Una cuenta no puede tener más de una lista de firmantes. Para eliminar una lista de firmantes, debes establecer `SignerQuorum` en `0` y omitir el campo `SignerEntries`. De lo contrario, la transacción falla con el error `temMALFORMED`. Una transacción para eliminar una lista de firmantes se considera exitosa incluso si no había ninguna lista de firmantes para eliminar.

Puedes crear, actualizar o eliminar una lista de firmantes utilizando la clave maestra, la clave regular o la lista de firmantes actual, si esos métodos para firmar transacciones están disponibles.

No puedes eliminar el último método de firma de transacciones de una cuenta. Si la clave maestra de una cuenta está deshabilitada (la cuenta tiene habilitada el flag `lsfDisableMaster`) y la cuenta no tiene configurada una clave regular, entonces no puedes eliminar la lista de firmantes de la cuenta. En su lugar, la transacción falla con el error `tecNO_ALTERNATIVE_KEY`.

Crear o reemplazar una lista de firmantes habilita el flag `lsfOneOwnerCount` en el objeto SignerList. Las listas que se crearon antes de que se habilitara la \[enmienda MultiSignReserveº][] no tienen este flag y tienen una reserva de propietario más alta. Puedes reducir la reserva de propietario para estas listas reemplazando la lista con la misma lista. Para obtener más información, consulta SignerList Flags.
