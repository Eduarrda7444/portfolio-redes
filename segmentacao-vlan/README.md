# Lab 02: Segmentação de Rede com VLANs (Cisco Packet Tracer)

**Projeto prático** no Cisco Packet Tracer simulando a segmentação lógica de uma rede de computadores através da criação de duas VLANs (Virtual Local Area Networks), demonstrando o isolamento de tráfego entre diferentes grupos de dispositivos em um mesmo switch.

## Topologia e Endereçamento

- **Switch**: Cisco 2960
- **VLAN 10 - SETOR A**:
  - PC0: 192.168.10.1/24
  - PC1: 192.168.10.2/24
- **VLAN 20 - SETOR B**:
  - PC2: 192.168.10.3/24

## Método de Configuração

### 1. Criação das VLANs no Switch

Acesse o switch e crie as VLANs através da interface gráfica ou CLI:

```
Switch> enable
Switch# configure terminal

! Criação das VLANs
Switch(config)# vlan 10
Switch(config-vlan)# name VLAN_10
Switch(config-vlan)# exit

Switch(config)# vlan 20
Switch(config-vlan)# name VLAN_20
Switch(config-vlan)# exit

! Atribuição das portas às VLANs
Switch(config)# interface fastEthernet 0/1
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

Switch(config)# interface fastEthernet 0/2
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 10
Switch(config-if)# exit

Switch(config)# interface fastEthernet 0/3
Switch(config-if)# switchport mode access
Switch(config-if)# switchport access vlan 20
Switch(config-if)# end
Switch# write memory
```

### 2. Configuração dos IPs dos Computadores

Atribua endereços IP estáticos a cada computador:

- **PC0 (VLAN 10)**: 
  - IP: 192.168.10.1
  - Máscara: 255.255.255.0
  - Gateway: (não configurado para testes locais)

- **PC1 (VLAN 10)**:
  - IP: 192.168.10.2
  - Máscara: 255.255.255.0
  - Gateway: (não configurado para testes locais)

- **PC2 (VLAN 20)**:
  - IP: 192.168.10.3
  - Máscara: 255.255.255.0
  - Gateway: (não configurado para testes locais)
