# Codificaciones en base 58

Las APIs de Xahau a menudo utilizan una codificación "base58" con una suma de verificación (a veces llamada "Base58Check") para representar direcciones de cuentas y otros tipos de valores relacionados con claves criptográficas. Esta codificación es la misma que [la utilizada para las direcciones de Bitcoin](https://en.bitcoin.it/wiki/Base58Check\_encoding), excepto que Xahau utiliza el siguiente diccionario: `rpshnaf39wBUDNEGHJKLM4PQRST7VWXYZ2bcdeCg65jkm8oFqi1tuvAxyz`.

Xahau agrega un número específico de 8 bits como prefijo a diferentes tipos de valores antes de codificarlos para distinguir entre diferentes tipos de datos. Con la disposición de caracteres en el diccionario base58 de Xahau, el resultado es que las representaciones base58 para diferentes tipos de valores codificados comienzan con letras específicas según su tipo.

La siguiente tabla enumera todas las codificaciones que Xahau utiliza:

| Tipo de Datos                               | Comienza con | Prefijo de tipo | Tamaño del contenido¹ | Caracteres máximos |
| ---------------------------------------- | ----------- | ----------- | ------------- | ------------------ |
| Dirección de cuenta                          | r           | `0x00`      | 20 bytes      | 35                 |
| Clave pública de cuenta                       | a           | `0x23`      | 33 bytes      | 53                 |
| Valor semilla (para claves secretas)             | s           | `0x21`      | 16 bytes      | 29                 |
| Clave pública de validación o de nodo | n           | `0x1C`      | 33 bytes      | 53                 |

¹ El tamaño del contenido excluye el prefijo de tipo de 1 byte.

### Ver también

* Codificación de Direcciones - información detallada sobre la codificación de direcciones.
* Claves Criptográficas - tipos de claves criptográficas en Xahau y cómo se utilizan.
* \[Referencia\_de\_wallet_propose]\[método\_wallet_propose] - Método API para generar claves de cuenta.
* \[Referencia\_de\_validation_create]\[método\_validation_create] - Método API para generar claves de validador.
