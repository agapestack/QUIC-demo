# QUIC demo

## Wireshark

```bash
export SSLKEYLOGFILE="/home/agape/Downloads/QUIC_DEMO/sslkeyfile"
chromium --no-sandbox
```

In wireshark:
Edit > Preferences > Protocols > TLS > (Pre)-Master-Secret log

## QLOG w/ curl

[Curl HTTP/3 documentation](https://github.com/curl/curl/blob/master/docs/HTTP3.md)

```bash
# Build quiche w/ boringssl
git clone --recursive -b 0.20.0 https://github.com/cloudflare/quiche
cd quiche
cargo build --package quiche --release --features ffi,pkg-config-meta,qlog
mkdir quiche/deps/boringssl/src/lib
ln -vnf $(find target/release -name libcrypto.a -o -name libssl.a) quiche/deps/boringssl/src/lib/
# build curl and config w/ quiche
cd ..
git clone https://github.com/curl/curl
cd curl
autoreconf -fi
./configure LDFLAGS="-Wl,-rpath,$PWD/../quiche/target/release" --with-openssl=$PWD/../quiche/quiche/deps/boringssl/src --with-quiche=$PWD/../quiche/target/release
make
make install
```

```bash
# Enable qlogging in curl by setting QLOGDIR
mkdir qlogs
export QLOGDIR="/home/agape/Downloads/QUIC_DEMO/qlogs"
./curl/src/curl --http3-only <target_url>
```
