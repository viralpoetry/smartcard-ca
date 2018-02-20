# smartcard-ca  

HSM Protected Certification Authority based on a forked [Easy-RSA v2.2.2](https://github.com/OpenVPN/easy-rsa/tree/2.2.2).  
Certification Authority (CA) private key is expected to be protected by PKCS11 enabled smartcard or Hardware Secure Module (HSM).  

Before you use this repository, make sure you have [initialized](USAGE.md) your PKCS11 enabled module, generated key pair, self-signed and loaded x509 certificate back to the module.  

Command line arguments
======================

There are two new command line arguments in the `pkitool`
```
--signinter   - sign an intermediate Certificate Authority CSR
--signserver  - sign TLS Web Server CSR
```
You can use them by executing following scripts:  
```
./sign-inter   <CSR name without the extension as stored in keys/ directory>
./sign-server  <CSR name without the extension as stored in keys/ directory>
```

Installation
============
Install openssl pkcs11 engine:  
```
sudo apt-get install libengine-pkcs11-openssl
```

Find out pkcs11 URI, or Slot & Object ID. This depends on the pkcs11 module used (Yubikey example):  
```
$ $ p11tool --provider ${PKCS11_MODULE_PATH} --list-tokens --no-detailed-url
Token 0:
	URL: pkcs11:model=PKCS%2315%20emulated;manufacturer=piv_II;serial=00000000;token=PIV%20Card%20Holder%20pin%20%28PIV_II%29
	Label: PIV Card Holder pin (PIV_II)
	Type: Hardware token
	Manufacturer: piv_II
	Model: PKCS#15 emulated
	Serial: 00000000
	Module: (null)

$ p11tool --login --list-privkeys pkcs11:model=PKCS%2315%20emulated;manufacturer=piv_II;serial=00000000;token=PIV%20Card%20Holder%20pin%20%28PIV_II%29
Token 'PIV Card Holder pin (PIV_II)' with URL 'pkcs11:model=PKCS%2315%20emulated;manufacturer=piv_II;serial=00000000;token=PIV%20Card%20Holder%20pin%20%28PIV_II%29' requires user PIN
Enter PIN:
Object 0:
	URL: pkcs11:model=PKCS%2315%20emulated;manufacturer=piv_II;serial=00000000;token=PIV%20Card%20Holder%20pin%20%28PIV_II%29;id=%02;object=SIGN%20key;type=private
	Type: Private key
	Label: SIGN key
	Flags: CKA_PRIVATE; CKA_ALWAYS_AUTH; CKA_NEVER_EXTRACTABLE; CKA_SENSITIVE;
	ID: 02
```

Set the appropriate values in the `vars` file:  
```
export PKCS11_SLOT=slot_0
export PKCS11_ID=id_2
```

Note that if you are using another `cryptoki` pkcs11 provider library, it is possible that you have to use `PKCS11_URI` instead of `PKCS11_ID`, `PKCS11_SLOT`.  

Known issues
============
If the openssl pkcs11 engine is installed to the wrong directory, link it to the correct one:  
```
sudo ln /usr/lib/x86_64-linux-gnu/openssl-1.0.2/engines/pkcs11.so /usr/lib/x86_64-linux-gnu/openssl-1.0.0/engines/
sudo ln /usr/lib/x86_64-linux-gnu/openssl-1.0.2/engines/libpkcs11.so /usr/lib/x86_64-linux-gnu/openssl-1.0.0/engines/
```
