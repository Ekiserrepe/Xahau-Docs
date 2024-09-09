# Datos del Ledger

Cada versión del ledger en el XRP Ledger está compuesta por tres partes:

* **Encabezado del Ledger**: Metadatos sobre esta versión del ledger.
* **Conjunto de transacciones**: Todas las transacciones que se ejecutaron para crear esta versión del ledger.
* **Datos de estado**: El registro completo de objetos que representan cuentas, configuraciones y saldos en esta versión del ledger. (Esto también se llama el "estado de la cuenta").

### Datos de Estado

\{{ page\_children(pages|selectattr("html", "eq", "ledger-object-types.html")|first, 1, 1, True) \}}
