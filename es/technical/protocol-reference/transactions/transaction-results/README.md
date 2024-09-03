# Resultados de transacciones

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/TER.h)

El servidor `rippled` resume los resultados de las transacciones con códigos de resultado, que aparecen en campos como `engine_result` y `meta.TransactionResult`. Estos códigos se agrupan en varias categorías con diferentes prefijos:

| Categoría              | Prefijo | Descripción                                                                                                                                                                                                   |
| --------------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Solo coste reclamado     | `tec`  | La transacción no logró su propósito, pero el costo de la transacción fue destruido. Este resultado solo es definitivo en un ledger validado.                                                                |
| Fallo               | `tef`  | La transacción no se puede aplicar al ledger actual (en progreso) del servidor ni a ningún otro posterior. Puede que ya se haya aplicado o que la condición del ledger impida aplicarla en el futuro. |
| Error local           | `tel`  | El servidor `rippled` tuvo un error debido a condiciones locales, como una carga alta. Puede obtener una respuesta diferente si reenvía la transacción a otro servidor o en otro momento.                                  |
| Transacción malformada | `tem`  | La transacción no fue válida debido a una sintaxis incorrecta, opciones conflictivas, una firma incorrecta o alguna otra razón.                                                                                               |
| Reintento                 | `ter`  | La transacción no se pudo aplicar, pero podría aplicarse con éxito en un ledger futuro.                                                                                                                     |
| Éxito               | `tes`  | (No es un error) La transacción fue exitosa. Este resultado solo es definitivo en un ledger validado.                                                                                                                       |

El servidor `rippled` reintenta automáticamente las transacciones fallidas. Es importante no asumir que una transacción ha fallado completamente basado en un resultado de fallo provisional. Una transacción puede tener éxito más tarde, a menos que su éxito o fallo sea definitivo.

**Advertencia:** Los códigos de resultado provisionales de las transacciones pueden diferir de su resultado final. Las transacciones que provisionalmente tuvieron éxito pueden eventualmente fallar, y las transacciones que provisionalmente fallaron pueden eventualmente tener éxito. Las transacciones que fallaron provisionalmente también pueden eventualmente fallar con un código diferente. Consulte la finalización de los resultados para saber cuándo el resultado de una transacción es definitivo.

La distinción entre un error local (`tel`) y una transacción mal formada (`tem`) es una cuestión de reglas a nivel de protocolo. Por ejemplo, el protocolo no establece un límite máximo en la cantidad de rutas que se pueden incluir en una transacción. Sin embargo, un servidor puede definir un límite finito de rutas que puede procesar. Si dos servidores diferentes están configurados de manera distinta, uno de ellos puede devolver un error `tel` para una transacción con muchas rutas, mientras que el otro servidor podría procesar la transacción con éxito. Si suficientes servidores pueden procesar la transacción para que sobreviva el consenso, entonces aún puede incluirse en un ledger validado.

En cambio, un error `tem` implica que ningún servidor en ningún lugar puede aplicar la transacción, independientemente de la configuración. O bien la transacción viola las reglas del protocolo, es inaceptablemente ambigua o es completamente incoherente. La única forma en que una transacción mal formada podría volverse válida es a través de cambios en el protocolo; por ejemplo, si se adopta una nueva función, las transacciones que usan esa función podrían considerarse mal formadas por servidores que ejecutan software más antiguo que no admite esa función.

### Respuesta Inmediata

La respuesta del \[método submit]\[] contiene un resultado provisional del servidor `rippled` que indica lo que sucedió durante el procesamiento local de la transacción.

La respuesta de `submit` contiene los siguientes campos:

| Campo                   | Valor          | Descripción                                                                                                                                               |
| ----------------------- | -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `engine_result`         | String         | Un código que indica el resultado de la transacción, como `tecPATH_DRY`.                                                                                  |
| `engine_result_code`    | Signed Integer | Un número que corresponde a `engine_result`. Los valores exactos están sujetos a cambios sin previo aviso.                                                  |
| `engine_result_message` | String         | Un mensaje legible que explica lo que sucedió. Este mensaje está destinado a que los desarrolladores diagnostiquen problemas y está sujeto a cambios sin previo aviso. |

Si no ocurrió nada incorrecto al enviar y aplicar la transacción localmente, la respuesta se ve así:

```js
    "engine_result": "tesSUCCESS",
    "engine_result_code": 0,
    "engine_result_message": "The transaction was applied. Only final in a validated ledger."
```

**Nota:** Un resultado exitoso en esta etapa no indica que la transacción haya tenido éxito completo; solo que se aplicó con éxito a la versión provisional del ledger mantenida por el servidor local. Los resultados fallidos en esta etapa también son provisionales y pueden cambiar. Consulte la Finalidad de los Resultados para obtener más detalles.
