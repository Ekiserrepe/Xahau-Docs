---
description: >-
  La transacción SetHook permite a los usuarios instalar, actualizar, eliminar o realizar otras operaciones en hooks en Xahau.
---

# SetHook

\[[Fuente](https://github.com/Xahau/xahaud/blob/dev/src/ripple/app/tx/impl/SetHook.cpp)]

_(Añadido por la \[enmienda Hooks]\[].)_

### Ejemplo

<pre class="language-json"><code class="lang-json">{
    "TransactionType": "SetHook",
    "Account": "rWYkbWkCeg8dP6rXALnjgZSjjLyih5NXm",
    "Flags": 0,
    "Hooks": [
<strong>        {
</strong><strong>            "Hook": {
</strong><strong>                "HookHash": "610F33B8EBF7EC795F822A454FB852156AEFE50BE0CB8326338A81CD74801864",
</strong>                "CreateCode": "697066733A2F2F4445414442454546697066733A2F2F44454144424545467878",
                "HookGrants": [],
                "HookNamespace": "0000000000000000000000000000000000000000000000000000000000000000",
                "HookParameters": [],
                "HookOn": "0000000000000000000000000000000000000000000000000000000000000000",
                "HookApiVersion": 0,
                "Flags": 0
<strong>            }
</strong>        }
    ]
}
</code></pre>

| Campo     | Tipo JSON | \[Tipo Interno]\[] | Descripción                                        |
| --------- | --------- | ------------------- | -------------------------------------------------- |
| `Account` | String    | AccountID           | La dirección de la cuenta que será propietaria del hook.
 |
| `Hooks`   | Array     | Array               | El array de hooks que se van a establecer.                      |

### Coste Especial de la Transacción

La transacción SetHook tiene un costo de transacción estándar, que es el costo mínimo requerido para todas las transacciones.

### Casos de error

Además de los errores que pueden ocurrir en todas las transacciones, las transacciones SetHook pueden resultar en los siguientes códigos de resultado de transacción:

| Código de error      | Descripción                                                                  |
| --------------- | ---------------------------------------------------------------------------- |
| `tecDUPLICATE`  | Ocurre si ya existe un hook con el mismo hash.                          |
| `tecDIR_FULL`   | Ocurre si el directorio del propietario está lleno y no puede acomodar el nuevo hook. |
| `terNO_ACCOUNT` | Ocurre si la cuenta de envío no existe.                                |
| `terNO_HOOK`    | Ocurre si no existe un hook con el hash especificado.                            |
| `temDISABLED`   | Ocurre si la enmienda Hooks no está habilitada.                                |
| `temMALFORMED`  | Ocurre si la transacción está mal formada.                                      |

### Operaciones de SetHook

Existen seis posibles operaciones: Sin Operación, Crear, Actualizar, Eliminar, Instalar y Eliminar Namespace.

Cada operación se especifica mediante la inclusión u omisión de ciertos campos del objeto HookSet. Esto puede parecer confuso al principio, pero al trabajar a través de algunos ejemplos, el lector debería encontrarlo intuitivo; esencialmente, las operaciones de HookSet son un tipo de **diferencia** entre los valores _predeterminados_, _existentes_ y los campos recién especificados de un Hook específico.

Lograr cada tipo de operación se explica en una subsección a continuación.

### Sin Operation

**Ocurre cuando**:

* El objeto HookSet está vacío

**Comportamiento**:

* No se realiza ningún cambio de ningún tipo.

**Ejemplo**:

JSON

```json
{ 
    Account: "r4GDFMLGJUKMjNhhycgt2d5LXCdXzCYPoc",
    TransactionType: "SetHook",
    Fee: "2000000",
    Hooks:
    [        
        {                        
            Hook: {}
        }
    ]
}
```

### Operación de Creación

**Ocurre cuando**:

_Todas_ las siguientes condiciones se cumplen:

* El Hook correspondiente no existe o se especifica `FLAG_OVERRIDE`.
* El campo `CreateCode` se especifica, no está en blanco y contiene el código de byte WebAssembly válido para un Hook válido.
* No existe ninguna instancia del mismo código de byte WebAssembly en XRPL. (Si lo hace y se cumplen todos los demás requisitos, entonces se interpreta como una operación de Instalación, ver a continuación).

**Comportamiento**:

* Se crea un objeto `HookDefinition` con recuento de referencias en XRPL que contiene los campos en el objeto HookSet, con todos los campos especificados (Namespace, Parameters, HookOn) convirtiéndose en predeterminados (pero no Grants).
* Se crea un array `Hooks` en la cuenta que ejecuta, si no existe ya. (Esta es la estructura que contiene los Hooks correspondientes).
* Se crea un objeto `Hook` en la posición correspondiente del Hook si aún no existe.
* El objeto `Hook` apunta al `HookDefinition`.
* El objeto `Hook` no contiene campos excepto `HookHash`, que apunta al `HookDefinition` creado.
* Si se especifica el flag `hsfNSDELETE`, entonces se eliminan todas las entradas de HookState en el namespace de destino si existen actualmente.

**Ejemplo**:

JSON

```json
{ 
    Account: "r4GDFMLGJUKMjNhhycgt2d5LXCdXzCYPoc",
    TransactionType: "SetHook",
    Fee: "2000000",
    Hooks:
    [        
        {                        
            Hook: {                
                CreateCode: fs.readFileSync('accept.wasm').toString('hex').toUpperCase(),
                HookOn: '0000000000000000',
                HookNamespace: addr.codec.sha256('accept').toString('hex').toUpperCase(),
                HookApiVersion: 0
            }
        }
    ]
}
```

### Operación de Instalación

**Ocurre cuando**:

_Todas_ las siguientes condiciones se cumplen:

* El Hook correspondiente no existe o se especifica `FLAG_OVERRIDE`.
* El campo `HookHash` se especifica, no está en blanco y contiene el hash de un Hook que ya existe como `HookDefinition` en el ledger _o_ el campo `CreateCode` se especifica, no está en blanco y contiene el código de byte WebAssembly válido para un hook válido que ya existe en el ledger como un `HookDefinition`.

**Comportamiento**:

* Se incrementa el recuento de referencias del objeto `HookDefinition`.
* Se crea un array `Hooks` en la cuenta que ejecuta, si no existe ya. (Esta es la estructura que contiene los Hooks correspondientes).
* Se crea un objeto `Hook` en la posición correspondiente del Hook si aún no existe.
* El objeto `Hook` apunta al `HookDefinition`.
* El objeto `Hook` contiene todos los campos en el objeto HookSet, excepto y a menos que:
* Un campo o par de claves dentro de un campo sea idéntico a los valores predeterminados del Hook establecidos en el `HookDefinition`, en cuyo caso se omite debido a los valores predeterminados.
* Si se especifica el flag `hsfNSDELETE`, entonces se eliminan todas las entradas de HookState en el namespace de destino si existen actualmente.

**Ejemplo**:

JSON

```json
{ 
    Account: "r4GDFMLGJUKMjNhhycgt2d5LXCdXzCYPoc",
    TransactionType: "SetHook",
    Fee: "2000000",
    Hooks:
    [        
        {                        
            Hook: {                
                HookHash: "A5663784D04ED1B4408C6B97193464D27C9C3334AAF8BBB4FA5EB8E557FC4A2C",
                HookOn: '0000000000000000',
                HookNamespace: addr.codec.sha256('accept').toString('hex').toUpperCase(),
            }
        }
    ]
}
```

### Operación de Actualización

**Cuando ocurre**:

_Todas_ las siguientes condiciones se cumplen:

* El Hook correspondiente existe.
* `HookHash` está ausente.
* `CreateCode` está ausente.
* Uno o más de `HookNamespace`, `HookParameters` o `HookGrants` están presentes.

**Comportamiento General**:

* El Hook correspondiente se actualiza de tal manera que los cambios deseados se reflejen en el Hook correspondiente.

**Comportamiento Específico**:

Si `HookNamespace` se especifica y difiere del Namespace del Hook correspondiente:

* El `HookNamespace` del Hook correspondiente se actualiza, y
* si se especifica el flag `hsfNSDELETE`, todas las entradas de HookState en el namespace anterior se eliminan.

Si se especifica `HookParameters`, entonces para cada entrada:

* Si `HookParameterName` existe pero `HookParameterValue` está ausente y los parámetros del Hook correspondiente (ya sea específicamente o a través de predeterminados) contienen este `HookParameterName`, entonces el parámetro se marca como eliminado en el Hook correspondiente.
* Si `HookParameterName` existe y `HookParameterValue` existe, entonces los parámetros del Hook correspondiente se modifican para incluir el nuevo o actualizado parámetro.

Si se especifica `HookGrants`, entonces:

* El array `HookGrants` del Hook correspondiente se reemplaza con el array.

**Ejemplo**:

JSON

```json
{ 
    Account: "r4GDFMLGJUKMjNhhycgt2d5LXCdXzCYPoc",
    TransactionType: "SetHook",
    Fee: "2000000",
    Hooks:
    [        
        {                        
            Hook: {
                HookNamespace: addr.codec.sha256('new_accept').toString('hex').toUpperCase(),
            }
        }
    ]
}
```

### Operación Delete

**Ocurre cuando**:

_Todas_ las siguientes condiciones se cumplen:

* El Hook correspondiente existe.
* Se especifica `hsfOVERRIDE`.
* opcionalmente también se especifica `hsfNSDELETE`.
* `HookHash` está ausente.
* `CreateCode` está presente pero vacío.

**Comportamiento**:

* Se decrementa el recuento de referencias del objeto `HookDefinition`.
* Si el recuento de referencias ahora es cero, el `HookDefinition` se elimina del ledger.
* El objeto `Hook` en la posición correspondiente del Hook se elimina, dejando una posición vacía.
* Si se especifica `hsfNSDELETE`, también se eliminan el namespace y todas las entradas de HookState.

**Ejemplo**:

JSON

```json
{ 
    Account: "r4GDFMLGJUKMjNhhycgt2d5LXCdXzCYPoc",
    TransactionType: "SetHook",
    Fee: "2000000",
    Hooks:
    [        
        {                        
            Hook: {
                CreateCode: "",
                Flags: 1,
            }
        }
    ]
}
```

### Reinicio de Namespace

**Ocurre cuando**:

_Todas_ las siguientes condiciones se cumplen:

* `flags` está presente y se establece `hsfNSDELETE`. `hsfOVERRIDE` también se puede especificar opcionalmente si se va a eliminar el Hook en esta posición.
* `HookNamespace` está especificado.
* `CreateCode` está ausente.
* `HookHash` está ausente.
* `HookGrants`, `HookParameters`, `HookOn` y `HookApiVersion` están ausente.

**Comportamiento**:

* Si el Hook correspondiente existe, permanece, no le pasa nada.
* Un subconjunto de objetos HookState y el directorio HookState para el namespace especificado se eliminan del ledger, hasta el límite definido (512). Se necesitan transacciones adicionales para continuar el proceso de eliminación hasta que se eliminen todos los registros relevantes. Ver [`tesPARTIAL`](https://docs.xahau.network/technical/protocol-reference/transactions/transaction-results/tes-codes).&#x20;

**Ejemplo**:

JSON

```json
{ 
    Account: "r4GDFMLGJUKMjNhhycgt2d5LXCdXzCYPoc",
    TransactionType: "SetHook",
    Fee: "2000000",
    Hooks:
    [        
        {                        
            Hook: {
              	HookNamespace: addr.codec.sha256('accept').toString('hex').toUpperCase(),
                Flags: 3,
            }
        }
    ]
}
```

### Campos de Hook

Los siguientes campos se utilizan en el objeto hook:


| Campos            | Tipo JSON | Tipo Interno | Descripción                               |
| ---------------- | --------- | ------------- | ----------------------------------------- |
| `HookHash`       | String    | Hash256       | El hash del hook.                     |
| `CreateCode`     | String    | Blob          | El código WebAssembly para el hook.        |
| `HookGrants`     | Array     | Array         | Los grants asociados con el hook.      |
| `HookNamespace`  | String    | Hash256       | El namespace del hook.                |
| `HookParameters` | Array     | Array         | Los parámetros del hook.               |
| `HookOn`         | String    | Hash256       | El evento en el que se activa el hook. |
| `HookApiVersion` | Number    | UInt16        | La versión de la API del hook.              |
| `Flags`          | Number    | UInt32        | Flags adicionales para el hook.            |

### Flags

El campo `Flags` en el objeto hook especifica flags adicionales para el hook. Se admiten los siguientes flags:

| Nombre Flag     | Descripción                                                              |
| ------------- | ------------------------------------------------------------------------ |
| `hsfOVERRIDE` | Permite que el hook sea eliminado incluso si está referenciado por otros objetos. |
| `hsfNSDELETE` | Elimina todo un namespace de hooks.                                    |
| `hsfCOLLECT`  | Recoge los objetos asociados al hook.                                  |

### Hook Grants

El campo `HookGrants` es un array de objetos que especifican los grants asociados con el hook. Cada objeto grant tiene los siguientes campos:

| Campo       | Tipo JSON | Tipo Interno | Descripción                                        |
| ----------- | --------- | ------------- | -------------------------------------------------- |
| `HookHash`  | String    | Hash256       | El hook al que se aplicará el grant.                    |
| `Authorize` | String    | AccountID     | La dirección de la cuenta a la que se le otorga acceso. |
| `Flags`     | Number    | Uint32        | Flags                                              |

### Parámetros Hook

El campo `HookParameters` es un array de objetos que especifican los parámetros del hook. Cada objeto parámetro tiene los siguientes campos:

| Campo   | Tipo JSON | Tipo Interno | Descripción                 |
| ------- | --------- | ------------- | --------------------------- |
| `Name`  | String    | Blob          | El nombre del parámetro.  |
| `Value` | String    | Blob          | El valor del parámetro. |

### Ejecuciones Hook

Cuando los Hooks se ejecutan, dejan información sobre el estado de esa ejecución. Esto aparece en los metadatos de la Transacción Originaria como un bloque `sfHookExecutions`. Este bloque contiene los siguientes campos:

| Campo                  | Tipo JSON | Tipo Interno | Descripción                                                                                                                                                  |
| ---------------------- | --------- | ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `HookAccount`          | String    | AccountID     | La cuenta en la que se ejecutó el Hook.                                                                                                                                 |
| `HookEmitCount`        | Number    | UInt16        | El número total de transacciones emitidas por el Hook.                                                                                               |
| `HookExecutionIndex`   | Number    | UInt16        | El SHA512H del Hook en el momento en que se ejecutó.                                                                                                         |
| `HookHash`             | String    | Hash256       | El valor del parámetro.                                                                                                                                  |
| `HookInstructionCount` | String    | UInt64        | El número total de instrucciones WebAssembly que se ejecutaron cuando se ejecutó el Hook.                                                                           |
| `HookResult`           | Number    | UInt8         | <p>Los Hooks pueden terminar de tres maneras: <code>accept</code>, <code>rollback</code> y <code>error</code>.
¡Esto <em>no</em> es lo mismo que sfHookReturnCode!</p> |
| `HookReturnCode`       | String    | UInt64        | El entero devuelto como tercer parámetro de `accept` o `rollback`.                                                                                       |
| `HookReturnString`     | String    | Blob          | La cadena devuelta en los dos primeros parámetros de `accept` o `rollback`, si los hay.                                                                           |
| `HookStateChangeCount` | Number    | UInt16        | El número de cambios de estado del Hook que hizo el Hook durante la ejecución.                                                                                             |

