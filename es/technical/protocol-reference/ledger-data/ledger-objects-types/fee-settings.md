# Configuración de tarifas (Fee Settings)

[\[Fuente\]](https://github.com/ripple/rippled/blob/master/src/ripple/protocol/impl/LedgerFormats.cpp#L115-L120)

El tipo de objeto `FeeSettings` contiene el coste base de transacción actual y las cantidades de reserva determinadas por la votación de tarifas. Cada versión del ledger contiene **como máximo** un objeto `FeeSettings`.

### Ejemplo JSON

Ejemplo de objeto `FeeSettings`:

```json
{
   "BaseFee": "000000000000000A",
   "Flags": 0,
   "LedgerEntryType": "FeeSettings",
   "ReferenceFeeUnits": 10,
   "ReserveBase": 20000000,
   "ReserveIncrement": 5000000,
   "XahauActivationLgrSeq": 0,
   "NetworkID": 21337,
   "AccountCount": 0,
   "index": "4BC50C9B0D8515D3EAAE1E74B29A95804346C491EE1A95BF25E4AAB854A6A651"
}
```

### Campos

El objeto `FeeSettings` tiene los siguientes campos:

| Nombre                    | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                      |
| ----------------------- | --------- | ------------------- | --------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| `BaseFee`               | String    | UInt64              | Sí       | El coste de transacción de la "transacción de referencia" en drops de XAH, en formato hexadecimal.                                                              |
| `Flags`                 | Number    | UInt32              | Sí       | Un mapa de bits de flags booleanos habilitados para este objeto. Actualmente, el protocolo no define ningún flag para los objetos `FeeSettings`. El valor es siempre `0`. |
| `LedgerEntryType`       | String    | UInt16              | Sí       | El valor `0x0073`, asignado a la cadena `FeeSettings`, indica que este objeto contiene la configuración de tarifas del ledger.                           |
| `ReferenceFeeUnits`     | Number    | UInt32              | Sí       | La `BaseFee` traducida en "unidades de tarifa".                                                                                                       |
| `ReserveBase`           | Number    | UInt32              | Sí       | La reserva base para una cuenta en Xahau, en drops de XAH.                                                                                       |
| `ReserveIncrement`      | Number    | UInt32              | Sí       | El incremento de la reserva para la propiedad de objetos, en drops de XAH.                                                                               |
| `XahauActivationLgrSeq` | Number    | UInt32              | No        | El índice del ledger donde se activó el génesis de Xahau.                                                                                              |
| `AccountCount`          | Number    | UInt32              | No        | El número de cuentas creadas en la red Xahau.                                                                                             |
| `NetworkID`             | Number    | UInt32              | No        | El ID de la red.                                                                                                                            |

**Advertencia:** El formato JSON para este tipo de objeto del libro mayor es inusual. Los campos `BaseFee`, `ReserveBase` y `ReserveIncrement` indican drops de XAH pero _**no**_ en el formato habitual para\[especificar XAH]\[Currency Amount].

Si la _\[enmienda XAHFees]\[]_ está habilitada, el objeto `FeeSettings` tiene estos campos en su lugar:

| Nombre                    | Tipo JSON | \[Tipo Interno]\[] | ¿Requerido? | Descripción                                                                                                                                     |
| ----------------------- | --------- | ------------------- | --------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `BaseFeeDrops`          | String    | Amount              | Sí       | El coste de transacción de la "transacción de referencia" en drops de XAH.                                                                            |
| `Flags`                 | Number    | UInt32              | Sí       | Un mapa de bits de flags booleanos habilitados para este objeto. Actualmente, el protocolo no define ningún flag para los objetos `FeeSettings`. El valor es siempre `0`. |
| `LedgerEntryType`       | String    | UInt16              | Sí       | El valor `0x0073`, asignado a la cadena `FeeSettings`, indica que este objeto contiene la configuración de tarifas del libro mayor.                          |
| `ReserveBaseDrops`      | String    | Amount              | Sí       | La reserva base para una cuenta en el Libro Mayor de XAH, en drops de XAH.                                                                             |
| `ReserveIncrementDrops` | String    | Amount              | Sí       | El incremento de la reserva para la propiedad de objetos, en drops de XAH.                                                                              |
| `XahauActivationLgrSeq` | Number    | UInt32              | No        | El índice del libro mayor donde se activó el génesis de Xahau.                                                                                             |
| `AccountCount`          | Number    | UInt32              | No        | El número de cuentas creadas en la red Xahau.                                                                                            |
| `NetworkID`             | Number    | UInt32              | No        |                                                                                                                                                 |

### Formato de ID de FeeSettings

El ID del objeto `FeeSettings` es el hash de la clave de espacio `FeeSettings` (`0x0065`) únicamente. Esto significa que el ID del objeto `FeeSettings` en un ledger es siempre:

```
4BC50C9B0D8515D3EAAE1E74B29A95804346C491EE1A95BF25E4AAB854A6A651
```
