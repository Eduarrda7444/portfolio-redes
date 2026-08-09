# 🌐 Lab 01: VPN IPsec Site-to-Site (Brasil - Japão)

Projeto prático no **Cisco Packet Tracer** simulando a interconexão segura de duas filiais (Brasil e Japão) através de um túnel de VPN IPsec sobre uma infraestrutura pública de Provedor de Internet.

---

## 📐 Topologia e Endereçamento

* **Site Brasil (LAN Privada):** `192.168.1.0/24` | **Gateway:** `192.168.1.1` | **PC0:** `192.168.1.10`
* **Site Japão (LAN Privada):** `10.0.0.0/24` | **Gateway:** `10.0.0.1` | **PC1:** `10.0.0.10`
* **WAN Pública Brasil:** `200.100.50.1/30`
* **WAN Pública Japão:** `200.100.60.2/30`

---

## 🛠️ Scripts de Configuração (Cisco IOS)

### 1. Roteador Brasil (`RT-BR`)

CISCO CLI
```
configure terminal

! ACL de Tráfego Interessante
access-list 100 permit ip 192.168.1.0 0.0.0.255 10.0.0.0 0.0.0.255

! Fase 1 (ISAKMP)
crypto isakmp policy 10
 encr aes
 authentication pre-share
 group 2
exit
crypto isakmp key SENHAVPN123 address 200.100.60.2

! Fase 2 (IPsec)
crypto ipsec transform-set MEU-SET esp-aes esp-sha-hmac

! Crypto Map
crypto map MEU-MAP 10 ipsec-isakmp
 set peer 200.100.60.2
 set transform-set MEU-SET
 match address 100
exit

! Aplicação na WAN
interface Serial0/3/0
 crypto map MEU-MAP
exit

end
write memory
```

### 2. Roteador Japão (RT-JP)

CISCO CLI
```
configure terminal

! ACL de Tráfego Interessante
access-list 100 permit ip 10.0.0.0 0.0.0.255 192.168.1.0 0.0.0.255

! Fase 1 (ISAKMP)
crypto isakmp policy 10
 encr aes
 authentication pre-share
 group 2
exit
crypto isakmp key SENHAVPN123 address 200.100.50.1

! Fase 2 (IPsec)
crypto ipsec transform-set MEU-SET esp-aes esp-sha-hmac

! Crypto Map
crypto map MEU-MAP 10 ipsec-isakmp
 set peer 200.100.50.1
 set transform-set MEU-SET
 match address 100
exit

! Aplicação na WAN
interface Serial0/3/1
 crypto map MEU-MAP
exit

end
write memory
```

## 🧪 Testes de Validação

Executados a partir do **PC0 (Brasil)** para o **PC1 (Japão - `10.0.0.10`)**:

* **Ping:** `ping 10.0.0.10` ➔ Valida a comunicação ponta a ponta entre as redes privadas.
* **Tracert:** `tracert 10.0.0.10` ➔ Valida que o tráfego atravessa o túnel criptografado ocultando os saltos da Internet.
