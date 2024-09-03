# Requisitos del nodo

Los requisitos de hardware para los nodos (RPC, validador, ...) generalmente deben alinearse con las especificaciones que se describen a continuación.

Para casos de uso específicos de nodos (por ejemplo, historial completo), las especificaciones pueden variar (y aumentar) con el tiempo.

### Los nodos deberían funcionar (razonablemente) bien para uso propio con:

* CPU: 6+ cores
* Memoria: 16GB+ (32GB recomendado)
* IO de disco: 10k random RW
* Espacio de disco: 100GB+
* Sistema de archivos: EXT4/XFS/...
* IO de la red: 100Mbit+

### Nodos para uso futuro (crecimiento de la red) en producción:

<table><thead><tr><th width="140"></th><th width="200">Mínimo</th><th width="199">Preferido</th><th>Ideal</th></tr></thead><tbody><tr><td>CPU</td><td>16 cores</td><td>20+ cores</td><td>40+ cores</td></tr><tr><td>Memoria</td><td>32GB</td><td>64GB</td><td>128GB+</td></tr><tr><td>IO Disco</td><td>15k random RW</td><td>20k random RW</td><td>30k random RW</td></tr><tr><td>Espacio de disco</td><td>500GB</td><td>1TB</td><td>2TB</td></tr><tr><td>Sistema de archivos</td><td>XFS</td><td>XFS</td><td>XFS</td></tr><tr><td>IO de Red</td><td>500Mbit+</td><td>1Gbit</td><td>10Gbit</td></tr></tbody></table>
