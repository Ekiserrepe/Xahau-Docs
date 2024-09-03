---
description: >-
  La transacción GenesisMint se utiliza para acuñar nuevos XAH y distribuirlos a múltiples cuentas. Esta transacción solo puede ser utilizada por la cuenta génesis.
---

# GenesisMint - (Transacción emitida)

\[[Fuente](https://github.com/ripple/rippled/blob/develop/src/ripple/app/tx/impl/GenesisMint.cpp)]

_Añadido por la \[enmienda XahauGenesis]\[] y la \[enmienda Hooks]\[]_

### Ejemplo

```json
{
    "TransactionType": "GenesisMint",
    "Account": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
    "GenesisMints": [
        {
            "GenesisMint": {
                "Destination": "rPT1Sjq2YGrBMTttX4GZHjKu9dyfzbpAYe",
                "Amount": "1000000000"
            },
        }
    ]
}
```

### Campos

| Campo        | Tipo JSON | Tipo Interno | Descripción                                                                      |
| ------------ | --------- | ------------- | -------------------------------------------------------------------------------- |
| Account      | String    | AccountID     | La dirección de la cuenta génesis que acuñará y distribuirá XAH.            |
| GenesisMints | Array     | Array         | Un array de objetos que representan los destinos y cantidades del XAH acuñado. |

### Objeto GenesisMint

La transacción GenesisMint incluye un arreglo de objetos llamados `GenesisMints`. Cada objeto representa una cuenta de destino y la cantidad de XAH que se acuñará y distribuirá a esa cuenta.

| Campo           | Tipo JSON | Tipo Interno | Descripción                                                                |
| --------------- | --------- | ------------- | -------------------------------------------------------------------------- |
| Destination     | String    | AccountID     | La dirección de la cuenta que recibirá el XAH acuñado.               |
| Amount          | String    | Amount        | La cantidad de XAH que se acuñará y distribuirá a la cuenta de destino. |
| GovernanceFlags | String    | Hash256       | _(Opcional)_ Los flags de gobernanza asociadas con la cuenta de destino. |
| GovernanceMarks | String    | Hash256       | _(Opcional)_ Los flags de gobernanza asociadas con la cuenta de destino. |

### Coste Especial de la Transacción

La transacción GenesisMint tiene un costo de transacción estándar, que es el costo mínimo requerido para todas las transacciones.

### Casos de Error

Además de los errores que pueden ocurrir en todas las transacciones, la transacción GenesisMint puede resultar en los siguientes códigos de resultado de transacción:

| Código de error     | Descripción                                                                                |
| -------------- | ------------------------------------------------------------------------------------------ |
| `temDISABLED`  | Ocurre si las enmiendas "Hooks" o "XahauGenesis" no están habilitadas.                        |
| `temMALFORMED` | Ocurre si la transacción está mal formada, como si faltan campos obligatorios o valores no válidos. |
