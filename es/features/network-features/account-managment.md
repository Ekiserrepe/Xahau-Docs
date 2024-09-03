# Gestión de cuentas

Las funciones de gestión de cuentas de la red Xahau son un componente crucial para que los usuarios gestionen sus cuentas con eficacia.

Incluye varios tipos de transacciones que permiten a los usuarios realizar varias operaciones en sus cuentas.

### Tipos de transacciones

#### AccountSet

La transacción `AccountSet` permite a los usuarios modificar las propiedades de sus cuentas. Esto incluye ajustes como la tasa de transferencia, los flags, y más.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/accountset.md" %}
[accountset.md](../../technical/protocol-reference/transactions/transaction-types/accountset.md)
{% endcontent-ref %}

#### AccountDelete

El tipo de transacción `AccountDelete` permite a los usuarios eliminar sus cuentas de la red Xahau. Esta operación es irreversible y debe utilizarse con precaución.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/accountdelete.md" %}
[accountdelete.md](../../technical/protocol-reference/transactions/transaction-types/accountdelete.md)
{% endcontent-ref %}

#### SetRegularKey

El tipo de transacción `SetRegularKey` permite a los usuarios establecer un par de claves regulares para su cuenta. Este par de claves puede utilizarse como alternativa al par de claves maestro para firmar transacciones.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/setregularkey.md" %}
[setregularkey.md](../../technical/protocol-reference/transactions/transaction-types/setregularkey.md)
{% endcontent-ref %}

#### SignersListSet

El tipo de transacción `SignersListSet` permite a los usuarios establecer una lista de firmantes para su cuenta. Esto resulta especialmente útil para las cuentas con varios firmantes, en las que es necesario que varias partes firmen las transacciones.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/signerlistset.md" %}
[signerlistset.md](../../technical/protocol-reference/transactions/transaction-types/signerlistset.md)
{% endcontent-ref %}

#### Import

El tipo de transacción `Import` se utiliza para importar transacciones de otras redes. Esta función es especialmente útil para los emisores que necesitan importar transacciones para sus titulares de activos. Se recomienda configurar las claves primero de sus cuentas antes de intentar importar transacciones.

Ten en cuenta que el proceso de importación para el emisor implica tipos de transacción específicos y requiere una configuración cuidadosa. Asegúrese siempre de que los hooks están correctamente configurados y de que las transacciones son válidas para las operaciones previstas.

{% content-ref url="../../technical/protocol-reference/transactions/transaction-types/import.md" %}
[import.md](../../technical/protocol-reference/transactions/transaction-types/import.md)
{% endcontent-ref %}

Estos tipos de transacción proporcionan a los usuarios un amplio conjunto de herramientas para gestionar sus cuentas en la red Xahau. Como con todas las operaciones, los usuarios deben asegurarse de que comprenden las implicaciones de cada tipo de transacción antes de utilizarla.
