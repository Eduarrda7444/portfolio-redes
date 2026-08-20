# Lab 04: Protocolos de Roteamento Dinâmico OSPF

Projeto prático desenvolvido no Cisco Packet Tracer simulando a infraestrutura de rede e a implementação do protocolo de roteamento dinâmico OSPF (Open Shortest Path First) em uma topologia em anel redundante para a interconexão de duas redes LAN atreladas aos roteadores Router0 e Router2.

O laboratório contempla a alocação de endereços IP estáticos, configuração de interfaces seriais via módulos HWIC-2T, formação de adjacências na Área 0 (Backbone) e validação da capacidade de convergência rápida e tolerância a falhas (failover automático).

---

## Topologia e Endereçamento Lógico

### Divisão da Infraestrutura
* **Rede WAN (Anel Backbone OSPF - Área 0):** Interconexão dos quatro roteadores (Router0, Router1, Router2 e Router3) utilizando links seriais redundantes para distribuição de rotas dinâmicas[cite: 1, 3].
* **Redes LAN (Acesso aos Hosts):** Segmento LAN 1 conectado à interface FastEthernet0/0 do Router0 (abrigando o PC0) e Segmento LAN 2 conectado à interface FastEthernet0/0 do Router2 (abrigando o PC1)[cite: 1, 3].

---

## Tabela de Endereçamento e Interfaces

| Dispositivo | Interface | Endereço IP | Máscara de Rede | Gateway Padrão | Observação |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **PC0** | FastEthernet0 | 192.168.10.2 | 255.255.255.0 | 192.168.10.1 | Rede LAN 1 |
| **PC1** | FastEthernet0 | 192.168.20.2 | 255.255.255.0 | 192.168.20.1 | Rede LAN 2[cite: 1] |
| **Router0** | FastEthernet0/0 | 192.168.10.1 | 255.255.255.0 | - | Gateway LAN 1[cite: 1] |
| **Router0** | Serial0/2/0 | 200.0.0.1 | 255.255.255.248 | - | Link para Router1[cite: 1] |
| **Router0** | Serial0/2/1 | 200.0.0.9 | 255.255.255.248 | - | Link para Router3[cite: 1] |
| **Router1** | Serial0/2/0 | 200.0.0.2 | 255.255.255.248 | - | Link para Router0[cite: 1] |
| **Router1** | Serial0/2/1 | 200.0.0.17 | 255.255.255.248 | - | Link para Router2[cite: 1] |
| **Router2** | FastEthernet0/0 | 192.168.20.1 | 255.255.255.0 | - | Gateway LAN 2[cite: 1] |
| **Router2** | Serial0/2/0 | 200.0.0.18 | 255.255.255.248 | - | Link para Router1[cite: 1] |
| **Router2** | Serial0/2/1 | 200.0.0.25 | 255.255.255.248 | - | Link para Router3[cite: 1] |
| **Router3** | Serial0/2/0 | 200.0.0.10 | 255.255.255.248 | - | Link para Router0[cite: 1] |
| **Router3** | Serial0/2/1 | 200.0.0.26 | 255.255.255.248 | - | Link para Router2[cite: 1] |

---

## Método de Configuração

### 1. Configuração do Router0

Acesse a CLI do Router0 para configurar as interfaces LAN e WAN, habilitar o processo OSPF e anunciar as redes pertencentes à Área 0:

```
Router> enable
Router# configure terminal

! Configuração da Interface LAN (PC0)
interface FastEthernet0/0
 ip address 192.168.10.1 255.255.255.0
 no shutdown
exit

! Configuração dos Links Seriais WAN
interface Serial0/2/0
 ip address 200.0.0.1 255.255.255.248
 clock rate 64000
 no shutdown
exit

interface Serial0/2/1
 ip address 200.0.0.9 255.255.255.248
 clock rate 64000
 no shutdown
exit

! Configuração do Processo OSPF
router ospf 1
 network 192.168.10.0 0.0.0.255 area 0
 network 200.0.0.0 0.0.0.7 area 0
 network 200.0.0.8 0.0.0.7 area 0
end

Router# write memory
```

2. Configuração do Router1

Acesse a CLI do Router1 para configurar as interfaces seriais de trânsito e o protocolo OSPF:

```
Router> enable
Router# configure terminal

interface Serial0/2/0
 ip address 200.0.0.2 255.255.255.248
 clock rate 64000
 no shutdown
exit

interface Serial0/2/1
 ip address 200.0.0.17 255.255.255.248
 clock rate 64000
 no shutdown
exit

router ospf 1
 network 200.0.0.0 0.0.0.7 area 0
 network 200.0.0.16 0.0.0.7 area 0
end

Router# write memory
```

3. Configuração do Router2

Acesse a CLI do Router2 para configurar a interface LAN (PC1), as interfaces seriais e o OSPF:

```
Router> enable
Router# configure terminal

! Configuração da Interface LAN (PC1)
interface FastEthernet0/0
 ip address 192.168.20.1 255.255.255.0
 no shutdown
exit

interface Serial0/2/0
 ip address 200.0.0.18 255.255.255.248
 clock rate 64000
 no shutdown
exit

interface Serial0/2/1
 ip address 200.0.0.25 255.255.255.248
 clock rate 64000
 no shutdown
exit

router ospf 1
 network 192.168.20.0 0.0.0.255 area 0
 network 200.0.0.16 0.0.0.7 area 0
 network 200.0.0.24 0.0.0.7 area 0
end

Router# write memory
```

4. Configuração do Router3

Acesse a CLI do Router3 para finalizar o anel redundante com a configuração das interfaces seriais e OSPF:

```
Router> enable
Router# configure terminal

interface Serial0/2/0
 ip address 200.0.0.10 255.255.255.248
 clock rate 64000
 no shutdown
exit

interface Serial0/2/1
 ip address 200.0.0.26 255.255.255.248
 clock rate 64000
 no shutdown
exit

router ospf 1
 network 200.0.0.8 0.0.0.7 area 0
 network 200.0.0.24 0.0.0.7 area 0
end

Router# write memory
```
