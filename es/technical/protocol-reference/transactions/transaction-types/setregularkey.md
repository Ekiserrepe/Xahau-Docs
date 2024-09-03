---
description: >-
  Una transacción SetRegularKey asigna, cambia o elimina el par de claves regular asociado con una cuenta.
---

# SetRegularKey

[\[Fuente\]](https://github.com/ripple/rippled/blob/4239880acb5e559446d2067f00dabb31cf102a23/src/ripple/app/transactors/SetRegularKey.cpp)

Puedes proteger tu cuenta asignando un par de claves regular y utilizándolo en lugar del par de claves maestro para firmar transacciones siempre que sea posible. Si tu par de claves regular se ve comprometido, pero no el par de claves maestro, puedes usar una transacción `SetRegularKey` para recuperar el control de tu cuenta.

### Ejemplo \{{currentpage.name\}} JSON

```json
{
    "Flags": 0,
    "TransactionType": "SetRegularKey",
    "Account": "rf1BiGeXwwQoi8Z2ueFYTEXSwuJYfV2Jpn",
    "Fee": "12",
    "RegularKey": "rAR8rR8sUkBoCZFawhkWzY4Y5YoyuznwD"
}
```

| Campo        | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                             |
| ------------ | --------- | ------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `RegularKey` | String    | AccountID           | _(Opcional)_ Una \[Address]\[] (dirección) codificada en base-58 que indica el par de claves regular que se asignará a la cuenta. Si se omite, se elimina cualquier par de claves regular existente de la cuenta. No debe coincidir con el par de claves maestro de la dirección. |

### Ver También

Para obtener más información sobre los pares de claves regulares y maestras, consulta Claves Criptográficas.

Para un tutorial sobre cómo asignar un par de claves regular a una cuenta, consulta Trabajando con un Par de Claves Regular.

Para una mayor seguridad, puedes usar firmas múltiples, pero la firma múltiple requiere más XAH para el \[coste de la transacción]\[] y la reserva.
