---
description: >-
  Una transacción ClaimReward permite a una cuenta reclamar las recompensas que ha acumulado. Las recompensas pueden ser reclamadas por el propietario de la cuenta o por un emisor especificado. La cuenta también puede optar por no recibir recompensas.
---

# ClaimReward

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/ClaimReward.cpp)]

_(Añadido por la \[enmienda BalanceRewards]\[].)_

### Optar por recibir recompensas + Reclamar

```json
{
    "TransactionType": "ClaimReward",
    "Account": "rWYkbWkCeg8dP6rXALnjgZSjjLyih5NXm",
    "Issuer": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh"
}
```

### Optar por no recibir recompensas

```json
{
    "TransactionType": "ClaimReward",
    "Account": "rWYkbWkCeg8dP6rXALnjgZSjjLyih5NXm",
    "Flags": 1
}
```

### Campos

| Campo     | Tipo JSON | \[Tipo Interno]\[] | Descripción                                             |
| --------- | --------- | ------------------- | ------------------------------------------------------- |
| `Account` | String    | AccountID           | La dirección de la cuenta que está reclamando la recompensa. |
| `Flags`   | Number    | UInt32              | _(Opcional)_ Puede tener el valor 1 para optar por no recibir recompensas. |
| `Issuer`  | String    | AccountID           | _(Opcional)_ La cuenta genesis.                       |

### Flags ClaimReward

Las transacciones del tipo ClaimReward admiten valores adicionales en el campo `Flags`, de la siguiente manera:

| Nombre del Flag  | Valor Hex    | Valor Decimal | Descripción                                                                                                                                                                                                           |
| ---------- | ------------ | ------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tfOptOut` | `0x00000001` | 1             | El flag `isOptOut` en el código de ClaimReward se utiliza para excluir una cuenta de recibir recompensas al eliminar los campos relacionados con recompensas del objeto de la cuenta en el ledger si el campo `sfFlags` en la transacción está configurado en 1. |

### Coste Especial de Transacción

La transacción ClaimReward tiene un costo de transacción estándar, que es el costo mínimo requerido para todas las transacciones.

### Casos de Error

Además de los errores que pueden ocurrir en todas las transacciones, las transacciones ClaimReward pueden resultar en los siguientes códigos de resultado de transacción:

| Código Error        | Descripción                                                                                             |
| ----------------- | ------------------------------------------------------------------------------------------------------- |
| `temDISABLED`     | Ocurre si la función no está habilitada.                                                                   |
| `temINVALID_FLAG` | Ocurre si el flag se establece en un valor distinto de 1.                                                      |
| `temMALFORMED`    | Ocurre si el emisor es el mismo que la cuenta de origen o si el flag y el emisor no están configurados correctamente.
 |
| `tecNO_ISSUER`    | Ocurre si el emisor no existe.                                                                    |
| `terNO_ACCOUNT`   | Ocurre si la cuenta remitente no existe.                                                           |
