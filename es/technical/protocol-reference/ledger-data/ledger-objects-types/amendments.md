# Enmiendas

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/impl/LedgerFormats.cpp#L138-L144)

El tipo de objeto `Amendments` contiene una lista de Enmiendas que están actualmente activas. Cada versión del ledger contiene **como máximo un** objeto `Amendments`.

### Ejemplo JSON

```json
{
    "Majorities": [
        {
            "Majority": {
                "Amendment": "1562511F573A19AE9BD103B5D6B9E01B3B46805AEC5D3C4805C902B514399146",
                "CloseTime": 535589001
            }
        }
    ],
    "Amendments": [
        "42426C4D4F1009EE67080A9B7965B44656D7714D104A72F9B4369F97ABF044EE",
        "4C97EBA926031A7CF7D7B36FDE3ED66DDA5421192D63DE53FFB46E43B9DC8373",
        "6781F8368C4771B83E8B821D88F580202BCB4228075297B19E4FDC5233F1EFDC",
        "740352F2412A9909880C23A559FCECEDA3BE2126FED62FC7660D628A06927F11"
    ],
    "Flags": 0,
    "LedgerEntryType": "Amendments",
    "index": "7DB0788C020F02780A673DC74757F23823FA3014C1866E72CC4CD8B226CD6EF4"
}
```

### Campos

| Nombre              | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                                                 |
| ----------------- | --------- | ------------------- | --------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `Amendments`      | Array     | Vector256           | No        | Array de IDs de enmiendas de 256 bits para todas las enmiendas actualmente habilitadas. Si se omite, no hay enmiendas habilitadas.                                                           |
| `Flags`           | Number    | UInt32              | Sí       | Un mapa de bits de flags booleanos habilitados para este objeto. Actualmente, el protocolo no define ningun flag para los objetos `Amendments`. El valor es `0`.                             |
| `LedgerEntryType` | String    | UInt16              | Sí       | El valor `0x0066`, mapeado en el string `Amendments`, indica que este objeto describe el estado de las enmiendas en Xahau.                                              |
| `Majorities`      | Array     | STArray             | No        | Array de objetos que describen el estado de las enmiendas que tienen apoyo mayoritario pero que aún no están habilitadas. Si se omite, no hay enmiendas pendientes con apoyo mayoritario. |

Cada miembro del campo `Majorities`, si está presente, es un objeto con un campo `Majority`, cuyo contenido es un objeto anidado con los siguientes campos:

| Nombre        | Tipo JSON | \[Tipo Intero]\[] | Descripción                                                                                        |
| ----------- | --------- | ------------------- | -------------------------------------------------------------------------------------------------- |
| `Amendment` | String    | Hash256             | El ID de la enmienda pendiente.                                                         |
| `CloseTime` | Number    | UInt32              | El campo `close_time` de la versión del ledger donde esta enmienda obtuvo más recientemente una mayoría. |

En el proceso de enmienda, un consenso de validadores añade una nueva enmienda al campo `Majorities` usando una pseudo-transacción \[EnableAmendment]\[] con el flag `tfGotMajority` cuando el 80% o más de los validadores la apoyan. Si el apoyo para una enmienda pendiente cae por debajo del 80%, una pseudo-transacción \[EnableAmendment]\[] con el flag `tfLostMajority` elimina la enmienda del array `Majorities`. Si una enmienda permanece en el campo `Majorities` durante al menos 2 semanas, una pseudo-transacción [EnableAmendment][] sin flags la elimina de `Majorities` y la añade permanentemente al campo `Amendments`.

**Nota:** Técnicamente, todas las transacciones en un ledger se procesan basándose en las enmiendas habilitadas en la versión del ledger inmediatamente anterior. Mientras se aplican transacciones a una versión del ledger donde una enmienda se habilita, las reglas no cambian a mitad del ledger. Después de que el ledger se cierra, el siguiente ledger usa las nuevas reglas definidas por las enmiendas que apliquen.

### Formato ID de las enmiendas

El ID del objeto `Amendments` es el hash de la clave de espacio `Amendments` (`0x0066`) únicamente. Esto significa que el ID del objeto `Amendments` en un ledger es siempre:

```
7DB0788C020F02780A673DC74757F23823FA3014C1866E72CC4CD8B226CD6EF4
```

(No confundas el ID del tipo de objeto `Amendments` en el ledger con el ID de enmienda de una enmienda individual.)
