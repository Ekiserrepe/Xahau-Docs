# Payments

La función de Payments (Pagos) de la red Xahau es un componente crucial que permite la transferencia de activos y fondos dentro de la red. Esta función está diseñada para facilitar las transacciones, asegurando un funcionamiento fluido y eficiente de la red.

### Tipos de transacciones

La función de Payments comprende varios tipos de transacciones, cada una de las cuales tiene un propósito único en la red. He aquí un breve resumen de cada tipo de transacción:

**DepositPreauth**

Este tipo de transacción permite a una cuenta preautorizar transacciones entrantes de una fuente especificada. Es una forma de crear una lista blanca de cuentas, garantizando que sólo se procesen las transacciones autorizadas.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/depositpreauth.md" %}
[depositpreauth.md](../../technical/protocol-reference/transactions/transaction-types/depositpreauth.md)
{% endcontent-ref %}

**TrustSet**

Este tipo de transacción permite a los usuarios crear una trust line (línea de confianza) con otra cuenta. Es una forma de establecer confianza entre dos cuentas, permitiéndoles realizar transacciones entre ellas.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/trustset.md" %}
[trustset.md](../../technical/protocol-reference/transactions/transaction-types/trustset.md)
{% endcontent-ref %}

**Payment**

Este es el tipo de transacción básico que permite la transferencia de activos entre cuentas. Es el tipo de transacción fundamental para cualquier operación de pago en la red.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/payment.md" %}
[payment.md](../../technical/protocol-reference/transactions/transaction-types/payment.md)
{% endcontent-ref %}

**PaymentChannelCreate**

Este tipo de transacción permite la creación de un canal de pago entre dos cuentas. Los canales de pago son soluciones de escalabilidad off-ledger que permiten transacciones de alta frecuencia y bajo coste entre dos partes.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/paymentchannelcreate.md" %}
[paymentchannelcreate.md](../../technical/protocol-reference/transactions/transaction-types/paymentchannelcreate.md)
{% endcontent-ref %}

**PaymentChannelFund**

Este tipo de transacción permite a una cuenta financiar un canal de pago existente. Es una forma de añadir más activos a un canal de pago, permitiendo que se realicen más transacciones.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/paymentchannelfund.md" %}
[paymentchannelfund.md](../../technical/protocol-reference/transactions/transaction-types/paymentchannelfund.md)
{% endcontent-ref %}

**PaymentChannelClaim**

Este tipo de transacción permite a una cuenta reclamar los fondos de un canal de pago. Es una forma de cerrar un canal de pago y recuperar los activos restantes.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/paymentchannelclaim.md" %}
[paymentchannelclaim.md](../../technical/protocol-reference/transactions/transaction-types/paymentchannelclaim.md)
{% endcontent-ref %}
