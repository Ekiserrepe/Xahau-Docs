# Cabecera Ledger

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/ledger/ReadView.h#L71)

Cada versión de un ledger tiene un encabezado único que describe su contenido. Puedes consultar la información del encabezado de un ledger con el \[método ledger]\[]. El contenido del encabezado del ledger es el siguiente:

| Campo                   | Tipo JSON | \[Tipo Interno]\[] | Descripción                                                                                                                                                                                                                                                      |
| ----------------------- | --------- | ------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `ledger_index`          | String    | UInt32              | El \[índice del ledger]\[] del ledger. Algunos métodos de API muestran esto como un entero entre comillas; otros lo muestran como un número JSON nativo.                                                                                                                    |
| `ledger_hash`           | String    | Hash256             | El \[SHA-512Half]\[] de esta versión del ledger. Esto sirve como un identificador único para este ledger y todo su contenido.                                                                                                                                           |
| `account_hash`          | String    | Hash256             | El \[SHA-512Half]\[] de la información del árbol de estado de este ledger.                                                                                                                                                                                                   |
| `close_time`            | Number    | UInt32              | El tiempo aproximado en que se cerró esta versión del ledger, en segundos desde la Ripple Epoch del 2000-01-01 00:00:00. Este valor se redondea según la `close_time_resolution`.                                                                             |
| `closed`                | Boolean   | Boolean             | Si es `true`, esta versión del ledger ya no acepta nuevas transacciones. (Sin embargo, a menos que esta versión del ledger esté validada, podría ser reemplazada por una versión diferente del ledger con un conjunto diferente de transacciones).                                             |
| `parent_hash`           | String    | Hash256             | El valor `ledger_hash` de la versión anterior del ledger que es el predecesor directo de esta. Si hay diferentes versiones del índice del ledger anterior, esto indica de cuál se derivó el ledger.                                           |
| `total_coins`           | String    | UInt64              | El número total de \[drops de XAH]\[] poseídos por cuentas en el ledger. Esto omite XAH que ha sido destruido por tarifas de transacción. La cantidad real de XAH en circulación es menor porque algunas cuentas son "agujeros negros" cuyas claves no son conocidas por nadie. |
| `transaction_hash`      | String    | Hash256             | El \[SHA-512Half]\[] de las transacciones incluidas en este ledger.                                                                                                                                                                                               |
| `close_time_resolution` | Number    | Uint8               | Un número entero en el rango \[2,120] que indica el número máximo de segundos por los que se podría redondear el `close_time`.                                                                                                                                            |
| `closeFlags`            | (Omitted) | UInt8               | Un mapa de bits de flags relacionados con el cierre de este ledger.                                                                                                                                                                                                       |

### Índice del Ledger

### Flags de cierre

El ledger tiene solo un flag definido para `closeFlags`: **`sLCF_NoConsensusTime`** (valor `1`). Si este flag está habilitado, significa que los validadores tuvieron diferentes tiempos de cierre para el ledger, pero construyeron el mismo ledger en otros aspectos, por lo que declararon consenso mientras "acordaban no estar de acuerdo" en el tiempo de cierre. En este caso, el valor oficial de `close_time` del ledger es 1 segundo después que el del ledger anterior.

El campo `closeFlags` no se incluye en ninguna representación JSON de un ledger, pero se incluye en la representación binaria de un ledger y es uno de los campos que determinan el hash del ledger.

### Ver también

Para conceptos básicos sobre ledgers, consulta Ledgers.
