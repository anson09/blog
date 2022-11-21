---
order: 22
tags: security
---

# ssl 安全

## Terms

- Digest: main content's hash result
- Signature: a encrypted digest by private key
- Certification: contain public key, meta info and all above's CA signature
- Passphrase: symmetric password
- Recipient: public key
- Identity: private key

## Features

传递密码：通过公钥传递密码 ( _Browser send symmetric secret key to server, it's not only efficiency concern, but also one pair of asymmetrical key can only take encrypt effect at one way_ ）

验证身份：能解密就能确认对方身份，因为当前公钥的 pair 只有一个人/组织会有。并且只存在解密成功与否，不存在解密结果是否正确

防止篡改：解出 digest 后，和主体内容的 hash 比较，如果不一致，就是被篡改了

## Tools

- [age](https://age-encryption.org) - Cli tool to encrypt and decrypt with asymmetrical key
- [acme.sh](https://acme.sh) - Free applying for server certificate and auto renew, using in server
- [mkcert](https://mkcert.dev) - Creating self-signed certificate, using in development
- [myssl](https://myssl.com/cert_decode.html) View your certification detail online
