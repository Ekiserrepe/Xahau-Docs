# Check

La función Check (Cheque) de la red Xahau es un sistema de pago diferido que permite la creación, cancelación y cobro de cheques dentro del libro mayor. Esta función está diseñada para facilitar transacciones seguras y eficientes entre las partes.

### Tipos de transacciones

#### CheckCreate

La transacción `CheckCreate` se utiliza para crear un objeto Cheque en el libro mayor. Esto representa un pago aplazado que puede ser cobrado por su destino previsto.&#x20;

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/checkcreate.md" %}
[checkcreate.md](../../technical/protocol-reference/transactions/transaction-types/checkcreate.md)
{% endcontent-ref %}

#### CheckCancel

La transacción `CheckCancel` se utiliza para cancelar un cheque que se ha creado pero que aún no se ha cobrado. Esto permite al remitente detener el procesamiento del pago si es necesario.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/checkcancel.md" %}
[checkcancel.md](../../technical/protocol-reference/transactions/transaction-types/checkcancel.md)
{% endcontent-ref %}

#### CheckCash

La transacción `CheckCash` se utiliza para cobrar un cheque que se ha creado. Esto permite al destinatario recibir los fondos que han sido aplazados.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/checkcash.md" %}
[checkcash.md](../../technical/protocol-reference/transactions/transaction-types/checkcash.md)
{% endcontent-ref %}
