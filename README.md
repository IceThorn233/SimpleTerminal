# st - <<simple>> terminal

st is a simple terminal emulator for X which sucks less.

## Requirements

 TODO: 

## Installtion

### Configure and build
``` sh
$ cmake -S. -Bbuild -G 'Ninja Multi-Config'
$ cmake --build build --config Release
```

### Install

Require root
```
# cmake --install build --config Release --prefix /usr/local
```

``` sh
$ cmake --install build --config Release --prefix "${HOME}/.local"
```
