# Lab 03: Projeto e Sistemas de Cabeamento Estruturado (Cisco Packet Tracer)

Projeto prático desenvolvido no Cisco Packet Tracer simulando a infraestrutura de rede e o planejamento de um Sistema de Cabeamento Estruturado (SCE) conforme a norma ABNT NBR 14565 para a empresa Atendimento Ágil Ltda., distribuída em dois pavimentos.

O laboratório contempla a segmentação lógica por VLANs, roteamento Inter-VLAN (Router-on-a-Stick), alocação dinâmica de endereços via DHCP e isolamento de tráfego de segurança para redes sensíveis (TEF e Visitantes)[cite: 2, 3].

---

## Topologia e Endereçamento Lógico

### Divisão dos Pavimentos
* **2º Andar (CPD / ER - Equipment Room):** Abriga o Rack Principal com Roteador (Router1), Switches centrais, Servidores, setores administrativos (Financeiro, RH, Compras, TI e Diretoria) e o Access Point Corporativo[cite: 2, 3].
* **1º Andar (TR - Telecommunications Room):** Abriga o Rack Secundário, setores operacionais (Atendimento ao Cliente, Gerente e Supervisor) e os Access Points para TEF e Visitantes[cite: 2, 3].

---

### Segmentação por VLANs e DHCP

| VLAN | Nome da Rede | Subrede / CIDR | Default Gateway | Tipo de Acesso | SSIDs / Segurança |
| :---: | :---: | :---: | :---: | :---: | :---: |
| **VLAN 10** | `CORPORATIVO` | `192.168.10.0/24` | `192.168.10.1` | Cabeado / Wi-Fi | SSID: `CORPORATIVO`<br>Chave: `Empresa2026#` |
| **VLAN 20** | `TEF` | `192.168.20.0/24` | `192.168.20.1` | Wi-Fi (Maquininhas) | SSID: `TEF`<br>Chave: `TEFEmpresa2026#` |
| **VLAN 30** | `VISITANTE` | `192.168.30.0/24` | `192.168.30.1` | Wi-Fi (Clientes) | SSID: `VISITANTE`<br>Segurança: `Disabled` (Aberta) |

---

## Método de Configuração

### 1. Configuração do Roteador Central (Router1)

Acesse a CLI do roteador central para criar o roteamento Inter-VLAN (Subinterfaces dot1Q) e os pools de distribuição dinâmica de IP via DHCP:

```
Router> enable
Router# configure terminal

! Reservar os IPs dos Gateways
ip dhcp excluded-address 192.168.10.1
ip dhcp excluded-address 192.168.20.1
ip dhcp excluded-address 192.168.30.1

! Pool DHCP - VLAN 10 (Corporativo)
ip dhcp pool POOL_CORP
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8
exit

! Pool DHCP - VLAN 20 (TEF)
ip dhcp pool POOL_TEF
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8
exit

! Pool DHCP - VLAN 30 (Visitantes)
ip dhcp pool POOL_GUEST
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8
exit

! Subinterfaces Roteamento Inter-VLAN (IEEE 802.1Q)
interface GigabitEthernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown
exit

interface GigabitEthernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown
exit

interface GigabitEthernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
 no shutdown
exit
```

2. Configuração de Trunk e Access nos Switches

Acesse a CLI dos Switches para criar a base de VLANs, configurar a porta Trunk com o Roteador e associar as portas de acesso dos dispositivos finais e Access Points:

```
Switch> enable
Switch# configure terminal

! Criação da base de VLANs
vlan 10
 name CORPORATIVO
vlan 20
 name TEF
vlan 30
 name VISITANTE
exit

! Configuração da Porta Trunk ligada ao Roteador/Switch-Principal
interface GigabitEthernet0/1
 switchport mode trunk
 no shutdown
exit

! Atribuição de portas de acesso para dispositivos Corporativos (VLAN 10)
interface range FastEthernet 0/1 - 15
 switchport mode access
 switchport access vlan 10
exit

! Atribuição da porta do AP TEF (VLAN 20)
interface FastEthernet 0/16
 switchport mode access
 switchport access vlan 20
exit

! Atribuição da porta do AP Visitantes (VLAN 30)
interface FastEthernet 0/17
 switchport mode access
 switchport access vlan 30
end

Switch# write memory
```
