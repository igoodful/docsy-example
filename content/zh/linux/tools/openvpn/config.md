---
title: 客户端配置详解
description: openvpn
date: 2023-11-17
weight: 30000


---

<style>
th, td {
  border: 1px solid rgb(190, 190, 190);
}
</style>

&#10060;

&#9989;

$ \rightleftharpoons $




| 配置项                 | 值            | 说明                                                         |
| :--------------------- | :------------ | :----------------------------------------------------------- |
| `client`               |               | 指定这是一个客户端                                           |
| `dev`                  | `tun`         |                                                              |
| `proto`                | `udp`、`tcp`  |                                                              |
| `remote`               | `ip port`     | `10.10.10.10 1194`                                           |
| `resolv-retry`         | `infinite`    | 启用该指令，与服务器连接中断后将自动重新连接                 |
| `nobind`               |               | 大多数客户端不需要绑定本机特定的端口号                       |
| `persist-key`          |               |                                                              |
| `persist-tun`          |               |                                                              |
| `ca`                   | `ca.crt`      |                                                              |
| `cert`                 | `client.crt`  |                                                              |
| `key`                  | `client.key`  |                                                              |
| `remote-cert-tls`      | `server`      | 要使用此功能，EasyRSA生成服务器证书的时候进行相关设置        |
| `tls-auth`             | `ta.key 1`    | 如果在服务器上使用tls-auth密钥，那么每个客户端也必须拥有密钥 |
| `cipher`               | `AES-256-CBC` | 选择一个加密算法，服务器使用的算法选项，也必须在这里指定它   |
| `comp-lzo`             |               | 在VPN连接中启用压缩                                          |
| `verb`                 | `3`           |                                                              |
| `mute`                 | `20`          |                                                              |
| `explicit-exit-notify` | `1`           |                                                              |
| `key-direction`        | `1`           | 将                                                           |























