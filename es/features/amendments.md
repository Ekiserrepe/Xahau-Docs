# Enmiendas

Las enmiendas representan nuevas características u otros cambios en el procesamiento de transacciones.

El sistema de enmiendas utiliza el proceso de consenso para aprobar cualquier cambio que afecte al procesamiento de transacciones en Xahau. Los cambios en el proceso de transacciones se introducen como enmiendas y los validadores votan sobre ellos. Si una enmienda recibe más del 80% de apoyo durante cinco días, la enmienda se aprueba y el cambio se aplica permanentemente a todas las versiones posteriores del libro mayor. Para desactivar una enmienda aprobada es necesario introducir una nueva enmienda.

**Nota:** Las correcciones de errores que cambian procesos de transacción también requieren enmiendas.

### Proceso de enmienda

El tema Aportar código a Xahau recorre el flujo de trabajo para desarrollar una enmienda desde una idea hasta su activación en Xahau.

Después de que el código para una enmienda se incorpora a una versión de software, el proceso para activarla se produce dentro de la red Xahau, que comprueba el estado de las enmiendas cada _flag_ ledger (normalmente cada 15 minutos).

Cada 256 ledgers se denomina **flag ledger**. El flag ledger no tiene un contenido especial, pero el proceso de modificación se desarrolla en torno a él.

1. **Flag Ledger -1:** Cuando los validadores de `xahaud` envían mensajes de validación, también envían sus votos de enmienda.
2. **Flag Ledger:** Los servidores interpretan los votos de los validadores de confianza.
3.  **Flag Ledger +1:** Los servidores insertan una pseudo-transacción `EnableAmendment` y un flag basado en lo que creen que ha ocurrido:

    * El flag `tfGotMajority` significa que la enmienda tiene más del 80% de apoyo.
    * El flag `tfLostMajority` significa que el apoyo a la enmienda ha disminuido al 80% o menos.
    * Ningún flag significa que la enmienda está habilitada.

    **Nota:** Es posible que una enmienda pierda el 80% de apoyo en el mismo libro contable en el que alcanza el periodo de cinco días requerido para ser habilitada. En estos casos, se añade una pseudo-transacción `EnableAmendment` para ambos escenarios, pero finalmente la enmienda se habilita.
4. **Flag Ledger +2:** Las enmiendas habilitadas se aplican a las transacciones de este ledger en adelante.

### Votación de enmiendas

Cada versión de `xahaud` se compila con una lista de enmiendas conocidas y el código para implementar dichas enmiendas. Los operadores de los validadores de `xahaud` configuran sus servidores para votar cada enmienda y pueden cambiarla en cualquier momento. Si el operador no elige un voto, el servidor utiliza un voto por defecto definido por el código fuente.

**Nota:** El voto por defecto puede cambiar entre versiones de software. \[Actualizado en: rippled 1.8.1]\[]

Las enmiendas deben mantener cinco días de apoyo de más del 80% de los validadores de confianza para ser habilitadas. Si el apoyo cae por debajo del 80%, la enmienda se rechaza temporalmente y se reinicia el periodo de dos semanas. Las enmiendas pueden ganar y perder la mayoría un número indefinido de veces antes de ser habilitadas de forma permanente.

Las enmiendas cuyo código fuente ha sido eliminado sin ser habilitadas se consideran **vetadas** por la red.

### Servidores Bloqueados por Enmiendas

El bloqueo de enmiendas es una característica de seguridad para proteger la exactitud de los datos de Xahau. Cuando se habilita una enmienda, los servidores que ejecutan versiones anteriores de `xahaud` sin el código fuente de la enmienda ya no entienden las reglas de la red. En lugar de adivinar e interpretar erróneamente los datos del libro mayor, estos servidores se convierten en **enmiendas bloqueadas** y no pueden:

* Determinar la validez de un libro mayor.
* Enviar o procesar transacciones.
* Participar en el proceso de consenso.
* Votar sobre futuras enmiendas.

La configuración de votación de un servidor `xahaud` no tiene ningún impacto en el bloqueo de enmiendas. Un servidor `xahaud` siempre sigue las enmiendas habilitadas por el resto de la red, por lo que los bloqueos se basan únicamente en tener el código para entender los cambios de reglas. Esto significa que también puedes ser bloqueado si conectas tu servidor a una red paralela con diferentes enmiendas habilitadas. Por ejemplo, la Xahau Testnet suele tener habilitadas enmiendas experimentales. Si está utilizando la última versión de producción, es probable que tu servidor no tenga el código para esas enmiendas experimentales.

Puedes desbloquear los servidores bloqueados por enmiendas actualizando a la última versión de `xahaud`.

### Retirada de enmiendas

Cuando las enmiendas están habilitadas, el código fuente de los comportamientos pre-enmienda permanecen en `xahaud`. Si bien hay casos de uso para mantener el código antiguo, como la reconstrucción de los resultados del libro mayor para la verificación, el seguimiento de las enmiendas y el código heredado añade complejidad con el tiempo.

El [XRP Ledger Standard 11d](https://github.com/XRPLF/XRPL-Standards/discussions/19) define un proceso para retirar enmiendas antiguas y el código pre-enmienda asociado. Después de que una enmienda haya estado habilitada en la Mainnet durante dos años, puede ser retirada. Retirar una enmienda la convierte en parte del protocolo central incondicionalmente; ya no se rastrea ni se trata como una enmienda, y se elimina todo el código previo a la enmienda.
