# Códigos TES

Los códigos `tesSUCCESS` y `tesPARTIAL` son los únicos que indican que una transacción tuvo éxito. Sin embargo, esto no siempre significa que logró lo que esperabas que hiciera. (Por ejemplo, una \[transacción OfferCancel]\[] puede "tener éxito" incluso si no hay ninguna oferta que cancelar)&#x20;

El código `tesPARTIAL` indica que una transacción ha tenido un éxito parcial. Específicamente, se utiliza en escenarios donde una operación, como la eliminación de registros de estado de hooks, excede un cierto límite (por ejemplo, 512 o 1024 registros). En tales casos, la transacción elimina hasta el límite y luego requiere que envíes transacciones adicionales con nuevos números de secuencia para continuar el proceso de eliminación hasta que se eliminen todos los registros. Esto garantiza que las operaciones grandes se dividan en partes manejables.

| Código         | Explicación                                                                                                                                               |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `tesSUCCESS` | La transacción se aplicó y se envió a otros servidores. Si esto aparece en un ledger validado, el éxito de la transacción es definitivo.               |
| `tesPARTIAL` | La transacción tuvo un éxito parcial y requiere transacciones adicionales para completar operaciones grandes, como eliminar más de un límite establecido de registros. |
