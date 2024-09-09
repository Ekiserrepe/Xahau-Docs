# Balance Rewards (Recompensas de saldo)

La función Balance Rewards (Recompensas de saldo) es un aspecto único de la red Xahau que permite a los usuarios acumular y reclamar recompensas en función del saldo de su cuenta. Esta función se implementa mediante una combinación de código nativo (Enmienda BalanceRewards) y código hook (Hook de rGenesis Account's Reward).

### Tipos de transacciones

#### ClaimReward

Una transacción `ClaimReward` permite a una cuenta reclamar las recompensas que ha acumulado. Las recompensas pueden ser reclamadas por el titular de la cuenta o por un emisor especificado. La cuenta también puede excluirse de las recompensas estableciendo el campo Flags en 1.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/claimreward.md" %}
[claimreward.md](../../technical/protocol-reference/transactions/transaction-types/claimreward.md)
{% endcontent-ref %}

#### GenesisMint

El tipo de transacción `GenesisMint` también está asociado a la función Balance Rewards. Se trata de una Transacción Emitida (Emitted) que se ejecuta a través del hook de recompensas cada vez que un usuario reclama recompensas de saldo.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/genesismint-emitted-txn.md" %}
[genesismint-emitted-txn.md](../../technical/protocol-reference/transactions/transaction-types/genesismint-emitted-txn.md)
{% endcontent-ref %}
