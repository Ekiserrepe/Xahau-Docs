# Escrow

La función Escrow es una parte crucial de la red Xahau. Proporciona un método seguro y fiable para las transacciones entre las partes. La función Escrow garantiza que los activos implicados en una transacción se mantengan de forma segura hasta que se cumplan todas las condiciones de la transacción.

### Tipos de transacciones

La función Escrow incluye tres tipos de transacciones:

#### EscrowCreate

La transacción `EscrowCreate` se utiliza para crear un nuevo acuerdo de custodia. Esta transacción especifica los términos de la custodia, incluidas las partes implicadas, los activos que se mantendrán en custodia y las condiciones en las que se liberarán los activos.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/escrowcreate.md" %}
[escrowcreate.md](../../technical/protocol-reference/transactions/transaction-types/escrowcreate.md)
{% endcontent-ref %}

#### EscrowFinish

La transacción `EscrowFinish` se utiliza para completar un acuerdo de custodia. Esta transacción se ejecuta cuando se cumplen todas las condiciones del escrow. Una vez ejecutada, los activos en custodia se entregan a la parte correspondiente.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/escrowfinish.md" %}
[escrowfinish.md](../../technical/protocol-reference/transactions/transaction-types/escrowfinish.md)
{% endcontent-ref %}

#### EscrowCancel

La transacción `EscrowCancel` se utiliza para cancelar un acuerdo de custodia. Esta transacción puede ejecutarse si no se cumplen las condiciones del escrow en un plazo determinado. Tras la cancelación, los activos en custodia se devuelven a la parte que inició la custodia.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/escrowcancel.md" %}
[escrowcancel.md](../../technical/protocol-reference/transactions/transaction-types/escrowcancel.md)
{% endcontent-ref %}
