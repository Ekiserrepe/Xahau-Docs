---
description: >-
  Xahau Mainnet permite ejecutar tu propio nodo local. Puedes ejecutar un contenedor Docker o conectarte a tu propia instancia local en ejecución.
---

# Ejecutando un nodo en la Mainnet

### Conectarse a la Mainnet de Xahau con Docker

Para conectarse a la Mainnet de Xahau, puedes utilizar este contenedor Docker:

{% embed url="https://github.com/Xahau/mainnet-docker" %}

1. Clonar el repositorio
2. Ejecutar `./build`
3. Ejecutar `./up`
4. Revisar el README para los comandos, etc. [https://github.com/Xahau/mainnet-docker/blob/main/README.md](https://github.com/Xahau/mainnet-docker/blob/main/README.md)
5. Conectarse utilizando WebSockets o RPC:\
   WebSocket: `ws://localhost:16006`\
   RPC (HTTP POST): `http://localhost:16007`

## Instancia local

### Información de Peering

<table data-header-hidden><thead><tr><th width="181"></th><th></th></tr></thead><tbody><tr><td>Info</td><td><a href="https://xahau.network/">https://xahau.network/</a></td></tr><tr><td>Explorador</td><td><a href="https://explorer.xahau.network/">https://explorer.xahau.network/</a></td></tr><tr><td>Documentos técnicos</td><td><a href="https://xrpl-hooks.readme.io/">https://xrpl-hooks.readme.io</a></td></tr><tr><td>WebSocket URL</td><td><a href="wss://xahau.network">wss://xahau.network</a></td></tr><tr><td>Peering público</td><td>Network ID: <strong><code>21337</code></strong><br>Peer 1: bacab.alloy.ee 21337</td></tr><tr><td>Binario y configuración</td><td><p>Para ubuntu 22.04:<br><br><strong>Descargar:</strong></p><p><a href="https://build.xahau.tech"><strong>https://build.xahau.tech</strong></a></p><p></p><p>Para los últimos números de versión de la build, consulta las <a href="https://github.com/Xahau/xahaud/actions?query=branch%3Arelease+is%3Asuccess+build+using+docker"><strong>Github Build Actions</strong></a>.</p><p><br>Extraer, luego ejecutar:<br><code>./xahaud --net --conf xahaud.cfg</code> Configurar el fichero de ejemplo (para ser guardado como <code>xahaud.cfg</code>):</p><ul><li><p><a href="https://github.com/Xahau/mainnet-docker/blob/main/store/etc/xahaud.sample.cfg">https://github.com/Xahau/mainnet-docker/blob/main/store/etc/xahaud.cfg</a></p><ul><li>Editar la ruta de la base de datos, etc. si es necesario.</li></ul></li><li>En la misma carpeta que <code>xahaud.cfg</code> config, the <code>validators-xahau.txt</code> file should be stored:<br><br><a href="https://github.com/Xahau/mainnet-docker/blob/main/store/etc/validators-xahau.sample.txt">https://github.com/Xahau/mainnet-docker/blob/main/store/etc/validators-xahau.txt</a></li></ul></td></tr><tr><td>UNL (VL)</td><td><a href="https://vl.xahau.org/">https://vl.xahau.org/</a></td></tr></tbody></table>
