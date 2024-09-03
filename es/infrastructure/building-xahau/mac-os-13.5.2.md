# Mac OS - 13.5.2

| Dependencia | Versión Funcional |
| ----------- | --------------- |
| Apple Clang | 14.0.3          |
| LLVM        | 14.0.3          |
| LLD         | 14.0.3          |
| Boost       | 1.77.0          |
| CMake       | 3.23.1          |
| Protobuf    | 3.20.0          |
| WasmEdge    | 0.11.2          |

### Establecer variables de entorno para la construcción

Primero, crea un directorio de dependencias. Me gusta usar `~/dependencies`

```
mkdir ~/dependencies
```

A continuación, necesitamos establecer las variables de entorno.

### Establecer variables de entorno para versiones

```
export LLVM_VERSION=14
export BOOST_VERSION=1.77.0
export WASMEDGE_VERSION=0.11.2
export PROTOBUF_VERSION=3.20.0
```

### Establecer variables de entorno para la construcción

```
export DEP_DIR=~/dependencies
export BOOST_FOLDER_NAME="boost_$(echo "$BOOST_VERSION" | sed 's/\./_/g')"
export BOOST_ROOT="$DEP_DIR/$BOOST_FOLDER_NAME"
export BOOST_LIBRARY_DIRS="$BOOST_ROOT/libs"
export BOOST_INCLUDE_DIR="$BOOST_ROOT/boost"
export BOOST_CXXFLAGS="${BOOST_CXXFLAGS:-} -DBOOST_ASIO_HAS_STD_INVOKE_RESULT"
export LLVM_PREFIX=`brew --prefix llvm@$LLVM_VERSION`
export LLVM_DIR="$LLVM_PREFIX/lib/cmake/llvm"
export LLVM_LIBRARY_DIR="$LLVM_PREFIX/lib"
export LLD_DIR="$LLVM_PREFIX/lib/cmake/lld"
export CC=clang
export CXX=clang++
export LDFLAGS="${LDFLAGS:-} -L$BOOST_LIBRARY_DIRS -L$LLVM_PREFIX/lib"
export CPPFLAGS="${CPPFLAGS:-} -I$BOOST_ROOT/include -I$LLVM_PREFIX/include"
export CFLAGS="${CFLAGS:-} -DBOOST_ASIO_HAS_STD_INVOKE_RESULT"
export CXXFLAGS="${CXXFLAGS:-} -DBOOST_ASIO_HAS_STD_INVOKE_RESULT"
```

### Instalar dependencias básicas

```
brew update
brew install automake \
             wget \
             curl \
             git \
             pkg-config \
             openssl \
             autoconf \
             libtool \
             unzip \
             cmake \
             "llvm@$LLVM_VERSION"
```

### Instalar dependencias de Rippled

Instalar Protobuf

```
cd $DEP_DIR && \
wget -nc https://github.com/protocolbuffers/protobuf/releases/download/v$PROTOBUF_VERSION/protobuf-all-$PROTOBUF_VERSION.tar.gz && \
tar -xzf protobuf-all-$PROTOBUF_VERSION.tar.gz && \
cd protobuf-$PROTOBUF_VERSION/ && \
./autogen.sh && \
./configure --prefix=/usr --disable-shared link=static && \
make -j$(sysctl -n hw.logicalcpu) && \
sudo make install
```

Instalar Boost

```
cd $DEP_DIR && \
wget https://boostorg.jfrog.io/artifactory/main/release/$BOOST_VERSION/source/$BOOST_FOLDER_NAME.tar.gz && \
tar -xvzf $BOOST_FOLDER_NAME.tar.gz && \
cd $BOOST_FOLDER_NAME && \
./bootstrap.sh && \
./b2 -j$(sysctl -n hw.logicalcpu)
```

Instalar WasmEdge

```
cd $DEP_DIR && \
wget -nc -q https://github.com/WasmEdge/WasmEdge/archive/refs/tags/$WASMEDGE_VERSION.zip && \
unzip -o $WASMEDGE_VERSION.zip && \
cd WasmEdge-$WASMEDGE_VERSION && \
mkdir build && \
cd build && \
cmake -DCMAKE_BUILD_TYPE=Release \
  -DWASMEDGE_BUILD_SHARED_LIB=OFF \
  -DWASMEDGE_BUILD_STATIC_LIB=ON \
  -DWASMEDGE_BUILD_AOT_RUNTIME=ON \
  -DWASMEDGE_FORCE_DISABLE_LTO=ON \
  -DCMAKE_POSITION_INDEPENDENT_CODE=ON \
  -DWASMEDGE_LINK_LLVM_STATIC=ON \
  -DWASMEDGE_BUILD_PLUGINS=OFF \
  -DWASMEDGE_LINK_TOOLS_STATIC=ON \
  .. && \
make -j$(sysctl -n hw.logicalcpu) && \
sudo make install
```

### Clonar el repositorio

```
mkdir ~/projects && \
cd ~/projects && \
git clone https://github.com/Xahau/xahaud.git && \
cd xahaud && \
git checkout dev
```

### Construir Xahaud

From the root `xahaud` directory:

```shellscript
mkdir build && \
cd build && \
cmake -DCMAKE_BUILD_TYPE=Debug -DLLVM_DIR=$LLVM_DIR -DLLVM_LIBRARY_DIR=$LLVM_LIBRARY_DIR .. && \
cmake --build . --target rippled --parallel -j$(sysctl -n hw.logicalcpu)
```
