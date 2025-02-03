# Generate a Key Pair

Dfns supports the following asymmetric key algorithms for credentials:

* ECDSA
* EDDSA
* RSA

Dfns supports many different curves. You can use any curve that is supported by Node JS' crypto library.

Dfns recommends the following curves / modulus lengths:

| Algorithm | Curve / Length                    |
| --------- | --------------------------------- |
| ECDSA     | secp256r1 (prime256v1 in OpenSSL) |
| EDDSA     | Ed25519                           |
| RSA       | 3072 bits                         |

### Examples <a href="#create-key-examples" id="create-key-examples"></a>

{% tabs %}
{% tab title="OpenSSL CLI" %}
```shell
# Recommended: Generate a EDDSA Private Key
# NOTE: This is not the key for the blockchain, only for the API.
# NOTE: EDDSA keys do not work in Postman!
openssl genpkey -algorithm Ed25519 -out ed25519key.pem
# Generate the Public Key
openssl pkey -in ed25519key.pem -pubout -out ed25519key.pub.pem

#OR 

# Generate a ECDSA Private Key
# NOTE: This is not the key for the blockchain, only for the API.
openssl ecparam -genkey -name prime256v1 -noout -out prime256v1.pem
# Generate the Public Key
openssl pkey -in prime256v1.pem -pubout -out prime256v1.pub.pem

#OR 

# Generate RSA Private Key
openssl genrsa -out rsa3072.pem 3072
# Generate the Public Key
openssl pkey -in rsa3072.pem -pubout -out rsa3072.pub.pem
```

On MacOS you may need to update your openssl version to add support for EDDSA keys

```shell
brew update
brew install openssl@1.1
echo 'export PATH="/usr/local/opt/openssl@1.1/bin:$PATH"' >> ~/.bash_profile
```
{% endtab %}

{% tab title="Node.js Crypto" %}
{% code title="crypto.ts" overflow="wrap" lineNumbers="true" %}
```typescript
import crypto from 'crypto'

// EDDSA Key
const eddsaKey = crypto.generateKeyPairSync('ed25519')
const eddsaPublicKey: string = eddsaKey.publicKey.export({ type: 'spki', format: 'pem' })
const eddsaPrivateKey: string = eddsaKey.privateKey.export({ type: 'pkcs8', format: 'pem' })

// ECDSA Key
const ecdsaKey: crypto.KeyPairKeyObjectResult = crypto.generateKeyPairSync('ec', { namedCurve: 'P-256' })
const ecdsaPublicKey: string = ecdsaKey.publicKey.export({ type: 'spki', format: 'pem' })
const ecdsaPrivateKey: string = ecdsaKey.privateKey.export({ type: 'pkcs8', format: 'pem' })

// RSA Key
const rsaKey: crypto.KeyPairKeyObjectResult = crypto.generateKeyPairSync('rsa', { modulusLength: 3072 })
const rsaPublicKey: string = rsaKey.publicKey.export({ type: 'pkcs1', format: 'pem' })
const rsaPrivateKey: string = rsaKey.privateKey.export({ type: 'pkcs1', format: 'pem' })
```
{% endcode %}
{% endtab %}

{% tab title="Web Crypto" %}
{% code title="crypto.js" overflow="wrap" lineNumbers="true" %}
```javascript
// Code taken from https://github.com/mdn/dom-examples/blob/main/web-crypto/export-key/pkcs8.js
function ab2str(buf) {
  return String.fromCharCode.apply(null, new Uint8Array(buf));
}

async function exportPrivateKey(key) {
  const exported = await window.crypto.subtle.exportKey('pkcs8', key)
  const exportedAsString = ab2str(exported)
  const exportedAsBase64 = window.btoa(exportedAsString)
  return `-----BEGIN PRIVATE KEY-----\n${exportedAsBase64}\n-----END PRIVATE KEY-----`
}

async function exportPublicKey(key, format) {
  const exported = await window.crypto.subtle.exportKey('spki', key)
  const exportedAsString = ab2str(exported)
  const exportedAsBase64 = window.btoa(exportedAsString)
  return `-----BEGIN PUBLIC KEY-----\n${exportedAsBase64}\n-----END PUBLIC KEY-----`
}

// EDDSA Key
/*
  EDDSA support in Web Crypto is experimental and may not be present in all browsers
  See: https://nodejs.org/api/webcrypto.html#ed25519ed448x25519x448-key-pairs
*/
const eddsaKey = await window.crypto.subtle.generateKey({ name: 'Ed25519' }, true, ['sign', 'verify'])
const eddsaPublicKey = await exportPublicKey(eddsaKey)
const eddsaPrivateKey = await exportPrivateKey(eddsaKey)

// ECDSA Key
const ecdsaKey = await window.crypto.subtle.generateKey({ name: 'ECDSA', namedCurve: 'P-256' }, true, ['sign', 'verify'])
const ecdsaPublicKey = await exportPublicKey(ecdsaKey)
const ecdsaPrivateKey = await exportPrivateKey(ecdsaKey)

// RSA Key
const rsaKey = await window.crypto.subtle.generateKey(
  {
    name: 'RSA-PSS',
    modulusLength: 3072,
    publicExponent: new Uint8Array([1, 0, 1]),
    hash: "SHA-256",
  },
  true,
  ['sign', 'verify']
)
const rsaPublicKey = await exportPublicKey(rsaKey)
const rsaPrivateKey = await exportPrivateKey(rsaKey)
```
{% endcode %}
{% endtab %}

{% tab title="AWS CLI" %}
```shell
# Generate a ECDSA Private Key
aws kms create-key --key-spec ECC_NIST_P256 --key-usage SIGN_VERIFY
# Get the Public Key
aws kms get-public-key --key-id <KEY_ID>

# Generate a RSA Private Key
aws kms create-key --key-spec RSA_3072 --key-usage SIGN_VERIFY
# Get the Public Key
aws kms get-public-key --key-id <KEY_ID>
```
{% endtab %}
{% endtabs %}

## Signature Format

An ECDSA (which includes EDDSA) signature consists of two values; a `r` and a `s`. Different crypto libraries use different formats for encoding these two values. The two most popular formats are `ASN.1 / DER format` and `raw format`.

Dfns APIs expects ECDSA/EDDSA signatures to use the `ASN. / DER` format.

### Raw Signatures

With `raw`, the `r` and `s` values are directly concatenated together to form the signature. Each value must be exactly 32 bytes long, with 0s added to the beginning of the `r/s` if it is less then 32 bytes.

### ASN.1 / DER

With `ASN.1 / DER`, the values are encoded using a deterministic format. The first byte is a magic value (`0x30`) that is used to identify the encoding. The second byte is the remaining length of the signature. The remaining bytes are the encoded `r` and `s` values. Each one is formated with its first byte being a magic value / separator (`0x02`). The second byte being the length of the value. And the remaining bytes being the value as a `minimal-sized signed big-endian hex number`. This means the number has all leading zeros removed, then the first byte must be a positive number (`0x00`-`0x7F`). If the minimized number starts with a negative value (`0x80`-`0xFF`) a zero is added to the beginning of the number. This means the final length of the value could be anywhere between 1 bytes and 33 bytes.

### Converting from Raw to ASN.1

If your encryption library uses `raw format` you can convert it to `ASN.1 DER` using the following code:

```javascript
function minimizeBigInt(value) {
  if (value.length === 0) {
    return value
  }
  const minValue = [0, ...value]
  for (let i = 0; i < minValue.length; ++i) {
    if (minValue[i] === 0) {
      continue
    }
    if (minValue[i] > 0x7f) {
      return minValue.slice(i-1)
    }
    return minValue.slice(i)
  }
  return new Uint8Array([0])
}

function rawSignatureToAns1(rawSignature) {
  if (rawSignature.length !== 64) {
    console.log(rawSignature.length)
    return new Uint8Array([0])
  }
  const r = rawSignature.slice(0, 32)
  const s = rawSignature.slice(32)

  const minR = minimizeBigInt(r)
  const minS = minimizeBigInt(s)

  return new Uint8Array([
    0x30,
    minR.length + minS.length + 4,
    0x02,
    minR.length,
    ...minR,
    0x02,
    minS.length,
    ...minS
  ])
}
```

## Base64 and Base64Url Encoding

Base64 is a popular way of encoding large values into a string that contains only printable characters.

Base64Url is an extension of Base64 encoding, but replaces characters that are not safe in URLs with different characters. It also minimizes the string, removing the extra padding characters. The Dfns auth system leverages Base64Url encoding in many different places.

In nodeJS, `Buffer` has built-in support for Base64Url as an encoding type.

```javascript
// Convert a string to a base64url string
Buffer.from('somerandomvalue').toString('base64url')
// Convert a base64url string to a string
Buffer.from('c29tZXJhbmRvbXZhbHVl', 'base64url').toString()
```

For places where `Buffer` is not available, or an older `Buffer` implementation is used, this code can be used to convert a value to a Base64Url encoded string:

```javascript
function arrayBufferToBase64(buffer) {
  const bytes = new Uint8Array(buffer)
  return btoa(String.fromCharCode(...bytes))
}

function arrayBufferToBase64Url(buffer) {
  return arrayBufferToBase64(buffer)
    .replace(/=/g, '')
    .replace(/\+/g, '-')
    .replace(/\//g, '_')
}
```
