---
description: Cómo reclamar un Ajuste de Balance en Xahau
---

# Ajustes de balance

### Optar + Reclamar

Optar por participar en Ajustes de Balance es la misma transacción que reclamar un ajuste. Debes hacer esto primero para activar la capacidad de reclamar más adelante.

```
{
    "Account": "<rAddr...>",
    "TransactionType": "ClaimReward",
    "Issuer": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
    "NetworkID": 21337
}    
```

### Optar por no participar

Para optar por no participar en Ajustes de Balance, omite el campo Issuer y establece Flags en 1. Al hacer esto, se eliminarán tus estadísticas de balance promedio. Estas no se restaurarán. Si vuelves a optar por participar, se restablecerán a una posición inicial.

```
{
    "Account": "<rAddr...>",
    "TransactionType": "ClaimReward",
    "NetworkID": 21337,
    "Flags" 1
} 
```

### Detalles técnicos

Los Ajustes de Balance se implementan como una combinación de dos piezas de código:

1. Enmienda de BalanceRewards (código nativo)
2. Hook de Recompensa de la Cuenta Génesis (código del hook).

BalanceRewards recopila estadísticas de balance promedio sobre las cuentas en las que están activadas. Estas estadísticas se pasan a un Hook objetivo cuando el usuario desea reclamar.

Todas las interacciones con esta enmienda se realizan a través de la transacción ClaimReward:

<pre><code><strong>{
</strong>    "Account": "&#x3C;rAddr...>",
    "TransactionType": "ClaimReward",
    "Issuer": "rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh",
    "NetworkID": 21337
}    
</code></pre>

Cuando una transacción ClaimReward se envía con éxito con un código de error **tesSUCCESS**, las estadísticas de balance promedio de la cuenta se restablecen. Lo único más que hace la enmienda es invocar los Hooks en la cuenta del Issuer especificado.

El campo _Issuer_ es la cuenta responsable de cumplir con la reclamación. Dependiendo de lo que esté instalado en la cuenta del Issuer, pueden suceder varias cosas:

* Si no hay un Hook instalado en la cuenta del Issuer, no se hace nada, y las estadísticas de BalanceRewards simplemente se restablecen.
* Si un Hook está instalado en la cuenta del Issuer y el Hook realiza un rollback, entonces la transacción falla, y las estadísticas _no_ se restablecen.
* Si un Hook está instalado en la cuenta del Issuer y el Hook realiza un accept, entonces la transacción tiene éxito y las estadísticas se restablecen. En este caso, el Hook también debería emitir una transacción de vuelta a la cuenta del usuario que contenga su recompensa.

En la práctica, en Xahau, el Issuer probablemente siempre será la cuenta génesis **rHb9CJAWyB4rj91VRWn96DkukG4bwdtyTh**.

A menos que:

* La Gobernanza de la Red resulte en el uso de una cuenta diferente para los Ajustes de Balance.
* Una parte no gubernamental realice su propio sorteo de BalanceRewards en el que el usuario desee participar. Ten en cuenta que las reclamaciones exitosas resultan en el restablecimiento de las estadísticas de balance promedio, por lo que solo se puede reclamar un tipo de recompensa por cada restablecimiento.

Las estadísticas de balance promedio existen como una colección de tres nuevos campos en el objeto AccountRoot. Estos son:

<table><thead><tr><th width="240">Campo</th><th width="95.33333333333331">Tipo</th><th>Explicación</th></tr></thead><tbody><tr><td><code>sfRewardAccumulator</code></td><td>UINT64</td><td>El área bajo el gráfico de balance-tiempo para tu cuenta desde la última transacción ClaimReward.</td></tr><tr><td><code>sfRewardLgrFirst</code></td><td>UINT32</td><td>El número de ledger de la última transacción ClaimReward.</td></tr><tr><td><code>sfRewardLgrLast</code></td><td>UINT32</td><td>El número de secuencia del ledger de la última transacción dentro o fuera de tu cuenta.</td></tr><tr><td><code>sfRewardTime</code></td><td>UINT32</td><td>El tiempo del libro mayor en el que se reclamó la última recompensa.</td></tr></tbody></table>



