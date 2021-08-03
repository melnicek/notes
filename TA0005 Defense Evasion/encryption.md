# Encryption

### Symmetric GPG encryption

```text
# encryption
gpg -c data.txt

# decryption
gpg -d data.txt.gpg
```

### Asymmetric encryption

```text
# generate private key
openssl genrsa -aes256 -out private.key 8912

# to generate public key we need private key
openssl rsa -in private.key -pubout -out public.key

# encrypting file with public key
openssl rsautl -encrypt -pubin -inkey public.key -in plaintext.txt -out encrypted.txt

# decrypting file with private key
openssl rsautl -decrypt -inkey private.key -in encrypted.txt -out plaintext.txt
```

