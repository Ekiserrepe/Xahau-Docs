# Hooks

Los Hooks son una potente característica de la red XRPL, proporcionando una robusta funcionalidad de contrato inteligente. Son pequeños y eficientes módulos WebAssembly diseñados específicamente para el XRPL, y pueden ser referidos como Contratos Inteligentes para el Protocolo Ledger XRP. Los Hooks pueden ser escritos en cualquier lenguaje que sea compilable con WebAssembly, permitiendo implementar una amplia gama de lógica de negocio y conceptos de contratos inteligentes.

### Tipos de transacciones

La función Hooks incluye dos tipos de transacciones:

**SetHook**

Este tipo de transacción se utiliza para establecer un hook en una cuenta.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/sethook.md" %}
[sethook.md](../../technical/protocol-reference/transactions/transaction-types/sethook.md)
{% endcontent-ref %}

**Invoke**

Este tipo de transacción se utiliza para llamar o invocar la funcionalidad de un hook.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/invoke.md" %}
[invoke.md](../../technical/protocol-reference/transactions/transaction-types/invoke.md)
{% endcontent-ref %}
