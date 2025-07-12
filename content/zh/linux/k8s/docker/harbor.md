---
title: harbor
description: harbor
date: 2025-06-16
weight: 100
viz: true

---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>



{{<alert color="primary">}}

【**思路**】



{{</alert>}}

---


```viz-dot
digraph "ca.key" {
rankdir = TB;
edge [
        fontsize="12"
        ];
node [
        shape = box;
        fontsize = "12"
];

"ca.key" -> "ca.crt";
"ca.key" -> "server.crt";
"server.key" -> "server.csr";
"server.csr" -> "server.crt";
"server.crt" -> "server.cert";
"ca.crt" -> "server.crt";
"v3.ext" -> "server.crt";

"server.crt" -> "harbor certificate/private_key";
"server.key" -> "harbor certificate/private_key";

"server.cert" -> "/etc/docker/certs.d/server/";
"server.key" -> "/etc/docker/certs.d/server/";
"ca.key" -> "/etc/docker/certs.d/server/";

}
```


docker程序认为"*.crt"文件是CA证书文件，"*.cert"客户端证书文件，于是上面第五步需要转换一下，其实使用cp一下也是可以的，内容并没有变化。

- `openssl genrsa -out ca.key 4096`
- `openssl req -x509 -new -nodes -sha512 -days 3650 -subj '/C=CN/ST=hubei/L=wuhan/O=igoodful/OU=cctv/CN=XXX' -key ca.key -out ca.crt`
  - XXX为主机名或IP地址
- `openssl genrsa -out XXX.key 4096`
- `openssl req -sha512 -new -subj '/C=CN/ST=hubei/L=wuhan/O=igoodful/OU=cctv/CN=XXX' -key XXX.key -out XXX.csr`
- `openssl x509 -req -sha512 -days 3650 -extfile v3.ext -CA ca.crt -CAkey ca.key -CAcreateserial -in XXX.csr -out XXX.crt`
- `openssl x509 -inform PEM -in XXX.crt -out XXX.cert`


#### `v3.ext`
> IP
```bash
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = IP:{{ IP }}

```

#### `v3.ext`
> hostname
```bash
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
extendedKeyUsage = serverAuth
subjectAltName = @alt_names

[alt_names]
DNS.1=XXX.com
DNS.2=XXX
DNS.3=hostname
```

