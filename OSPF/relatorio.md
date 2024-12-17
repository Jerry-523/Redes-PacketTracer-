# Documentação Completa da Configuração de Rede com OSPF e ACLs Estendidas

## 1. **Objetivo**

Configurar uma rede Cisco utilizando os seguintes requisitos:

- Configuração básica em todos os dispositivos.
- Configuração de roteamento dinâmico usando OSPF.
- Configuração de ACLs estendidas para controlar o acesso entre as redes conforme os requisitos:

### Requisitos de ACL:

1. **PC0** pode acessar os routers via Telnet.
2. Nenhum PC da **LAN Praia** pode acessar a **LAN Mindelo**.
3. **PC4** não pode acessar os servidores (LAN Assomada).
4. **PC4** apenas pode acessar o **Server0** via HTTP.
5. **PC2** pode acessar o **Server1** apenas via FTP.
6. **PC3** não pode acessar o **Server2** via FTP, mas deve ter todos os outros acessos.

---

## 2. **Topologia**

A topologia é composta por:

- **LAN Mindelo**: 192.168.1.0/24
- **LAN Praia**: 192.168.2.0/24
- **LAN Assomada**: 192.168.3.0/24
- **Servidor Server0**: 192.168.3.2
- **Servidor Server1**: 192.168.3.3
- **Servidor Server2**: 192.168.3.4
- **Roteadores**: Configuração OSPF e ACLs

---

## 3. **Configuração Básica**

### **Configuração de Interface no Router 3**

```plaintext
interface g0/2
ip address 192.168.2.1 255.255.255.0
no shutdown

interface g0/1
ip address 192.168.0.2 255.255.255.252
no shutdown
```

---

## 4. **Configuração OSPF**

Configure o **roteamento dinâmico OSPF** em todos os roteadores para garantir conectividade entre as redes.

### Exemplo de Configuração OSPF em Router3:

```plaintext
enable
configure terminal
router ospf 1
network 192.168.2.0 0.0.0.255 area 0
network 192.168.0.0 0.0.0.3 area 0
exit
```

### Verificação do OSPF:

- Comando para verificar as rotas OSPF:
  ```plaintext
  show ip route ospf
  ```
- Comando para verificar os vizinhos OSPF:
  ```plaintext
  show ip ospf neighbor
  ```

---

## 5. **Configuração das ACLs Estendidas**

Aqui estão as **ACLs estendidas** conforme os requisitos.

### 5.1 **Permitir que apenas o PC0 acesse os roteadores via Telnet**

**IP do PC0**: `192.168.1.2`

```plaintext
enable
configure terminal
line vty 0 4
password 1234
login
access-class 10 in
exit

access-list 10 permit 192.168.1.2
access-list 10 deny any

```

### 5.2 **Bloquear o acesso da LAN Praia para a LAN Mindelo**

**LAN Praia**: `192.168.2.0/24`
**LAN Mindelo**: `192.168.1.0/24`

```plaintext
access-list 120 deny ip 192.168.2.0 0.0.0.255 192.168.1.0 0.0.0.255
access-list 120 permit ip any any

interface g0/2
ip access-group 120 out

```

---

### 5.3 **Impedir que o PC4 acesse todos os servidores da LAN Assomada**

**PC4**: `192.168.2.3`
**LAN Assomada**: `192.168.3.0/24`

```plaintext
access-list 130 deny ip host 192.168.2.3 192.168.3.0 0.0.0.255
access-list 130 permit ip any any

interface g0/2
ip access-group 130 out

```

---

### 5.4 **Permitir que o PC4 acesse apenas o Server0 via HTTP**

**Server0**: `192.168.3.2`
**Porta HTTP**: `80`

```plaintext
access-list 140 permit tcp host 192.168.2.3 host 192.168.3.2 eq 80
access-list 140 deny ip host 192.168.2.3 any
access-list 140 permit ip any any

interface g0/2
ip access-group 140 in

```

---

### 5.5 **Permitir que o PC2 acesse o Server1 apenas via FTP**

**PC2**: `192.168.1.4`
**Server1**: `192.168.3.3`
**Porta FTP**: `21`

```plaintext
access-list 150 permit tcp host 192.168.1.4 host 192.168.3.3 eq 21
access-list 150 deny ip host 192.168.1.4 any
access-list 150 permit ip any any

interface g0/2
ip access-group 150 in

```

---

### 5.6 **Impedir que o PC3 acesse o Server2 via FTP, mas permitir todo o resto**

**PC3**: `192.168.2.2`
**Server2**: `192.168.3.4`
**Porta FTP**: `21`

```plaintext
access-list 160 deny tcp host 192.168.2.2 host 192.168.3.4 eq 21
access-list 160 permit ip any any

interface g0/2
ip access-group 160 in

```

---

## 6. **Verificação das ACLs**

### Verificar ACLs aplicadas:

```plaintext
show access-lists
```

### Verificar interfaces com ACLs:

```plaintext
show ip interface
```

## 7. **Conclusão**

- **Configuração básica** de dispositivos Cisco.
- **Configuração de OSPF** para roteamento dinâmico.
- **Configuração de ACLs estendidas** para atender às políticas de acesso especificadas.

As configurações garantem segurança e controle de acesso entre os dispositivos e redes.
