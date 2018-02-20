# Examples

Yubikey
============

Setup Test Root CA:  
```
# yubikey default vars
export pin=123456
export puk=12345678
export key=010203040506070801020304050607080102030405060708

# generate RSA key
openssl genrsa -out root_ca.key 2048

# generate CSR
openssl req -sha256 -new -config openssl-1.0.0.cnf -key root_ca.key -nodes -out root_ca.csr

# generate self-signed CRT
openssl req -new -sha256 -x509 -set_serial 1 -days 1000000 -config openssl-1.0.0.cnf -key root_ca.key -out root_ca.crt

# import crt, key to the Yubikey
yubico-piv-tool --key=$key -a import-key -s 9c < root_ca.key
yubico-piv-tool --key=$key -a import-certificate -s 9c < root_ca.crt

# copy cert to the keys directory
cp root_ca.crt $KEY_DIR/ca.crt
```

Create test CSR
```
openssl genrsa -out $KEY_DIR/domain.com.key 4096
openssl req -new -sha256 -key $KEY_DIR/domain.com.key -out $KEY_DIR/domain.com.csr
```

Sign CSR as an intermediate CA
```
. vars
./sign-inter domain.com
```

Or, sign CSR for TLS Web server
```
. vars
./sign-server domain.com
```
