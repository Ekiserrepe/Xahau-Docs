---
description: >-
  Xahau Testnet permite ejecutar tu propio nodo local. Puedes ejecutar un contenedor Docker o conectarte a tu propia instancia local en ejecución.
---

# Conectarse a Xahau Testnet

### Conectarse a la Testnet de Xahau con Docker

Para conectarte a la Testnet de Xahau (Testnet de Hooks V3), puedes utilizar este contenedor Docker:

{% embed url="https://github.com/Xahau/Xahau-Testnet-Docker/" %}

1. Clonar el repositorio
2. Ejecutar `./build`
3. Ejecutar `./up`
4. Consulta el README para comandos, etc.\
   [https://github.com/Xahau/Xahau-Testnet-Docker/blob/main/README.md](https://github.com/Xahau/Xahau-Testnet-Docker/blob/main/README.md)
5. Conectar usando WebSockets o RPC:\
   WebSocket: `ws://localhost:16006`\
   RPC (HTTP POST): `http://localhost:16007`

## Instancia local

### Información de peering

<table data-header-hidden><thead><tr><th width="181"></th><th></th></tr></thead><tbody><tr><td>Info y Faucet</td><td><a href="https://xahau-test.net/">https://xahau-test.net</a></td></tr><tr><td>Explorador</td><td><a href="https://explorer.xahau-test.net/">https://explorer.xahau-test.net</a></td></tr><tr><td>Documentos técnicos</td><td><a href="https://xrpl-hooks.readme.io/">https://xrpl-hooks.readme.io</a></td></tr><tr><td>WebSocket URL</td><td><a href="https://xahau-test.net/">wss://xahau-test.net</a></td></tr><tr><td>Public peering</td><td>Network ID: <strong><code>21338</code></strong><br>Peer 1: <code>159.65.199.56</code> Port: 21338<br>Peer 2: <code>139.59.98.59</code> Port: 21338<br>Peer 3: <code>88.99.3.241</code> Port: 21338<br>Peer 4: <code>88.99.4.45</code> Port: 21338</td></tr><tr><td>Binario y configuración</td><td><p>Para ubuntu 22.04:<br><br><strong>Descarga:</strong></p><p><a href="https://build.xahau.tech/2023.9.25-dev%2B336"><strong>https://build.xahau.tech/2023.9.25-dev%2B336</strong></a></p><p><br>Extraer y ejecutar: <br><code>./xahaud --net --quorum=5 --conf xahaud.cfg</code> Configurar fichero de ejemplo (para ser guardado como <code>xahaud.cfg</code>):</p><ul><li><p><a href="https://github.com/Xahau/Xahau-Testnet-Docker/blob/main/store/etc/xahaud.cfg">https://github.com/Xahau/Xahau-Testnet-Docker/blob/main/store/etc/xahaud.cfg</a></p><ul><li>Editar la ruta de la base de datos, etc si es necesario.</li></ul></li><li>En el mismo directorio que <code>xahaud.cfg</code>, el archivo <code>validators-xahau.txt</code> deberá ser guardado:<br><br><a href="https://github.com/Xahau/Xahau-Testnet-Docker/blob/main/store/etc/validators-xahau.txt">https://github.com/Xahau/Xahau-Testnet-Docker/blob/main/store/etc/validators-xahau.txt</a></li></ul></td></tr><tr><td>Validadores</td><td><code>[validators] nHDs6fHVnhb4ZbSFWF2dTTPHoZ6Rr39i2UfLotzgf8FKUi7iZdxx nHUvgFxT8EGrXqQZ1rqMw67UtduefbaCHiVtVahk9RXDJP1g1mB4 nHU7Vn6co7xEFMBesV7qw7FXE8ucKrhVWQiYZB5oGyMhvqrnZrnJ nHBoJCE3wPgkTcrNPMHyTJFQ2t77EyCAqcBRspFCpL6JhwCm94VZ nHUVv4g47bFMySAZFUKVaXUYEmfiUExSoY4FzwXULNwJRzju4XnQ nHBvr8avSFTz4TFxZvvi4rEJZZtyqE3J6KAAcVWVtifsE7edPM7q nHUH3Z8TRU57zetHbEPr1ynyrJhxQCwrJvNjr4j1SMjYADyW1WWe nHBdSXv3DhYJVXUppMLpCwJWDFVQyFdZrbMxeh8CFiBEvfTCy3Uh nHUJbfgaAtkbuAdtrqCDerP99PcprcpyqakZTsYpkQdb67aKKyJn</code></td></tr><tr><td>UNL (VL)</td><td><a href="https://vl.test.xahauexplorer.com">vl.test.xahauexplorer.com</a></td></tr></tbody></table>



### Integración de Xumm con Hooks V3

<figure><img src="../../.gitbook/assets/image.png" alt="" width="145"><figcaption></figcaption></figure>

Para conectar Xumm a la red de staging de Hooks V3, escanea este código QR con el escaner QR en Xumm para agregar el endpoint (de encima) a la lista de nodos de Xumm.
