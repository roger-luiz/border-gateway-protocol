# BGP - Guia Completo

## 📌 1. O que é o BGP?

**BGP (Border Gateway Protocol)** é o **protocolo de roteamento entre sistemas autônomos (AS - Autonomous Systems)** utilizado na Internet. Ele é um **protocolo de roteamento externo (EGP - Exterior Gateway Protocol)** e é definido na **RFC 4271**.

BGP é baseado em **vetor de caminho (path vector)** — ou seja, além do próximo salto, ele informa todo o caminho (sequência de ASs) até o destino.

### Tipos de BGP:
- **eBGP (External BGP)**: entre ASs diferentes.
- **iBGP (Internal BGP)**: entre roteadores dentro do mesmo AS.

---

## 🧠 2. Por que usar BGP?

Porque ele oferece:
- Controle preciso sobre o **roteamento entre redes diferentes** (ex.: entre ISPs).
- Capacidade de aplicar **políticas de roteamento avançadas** (prefiro esse caminho, evite esse outro).
- **Escalabilidade**, essencial para a Internet global.
- Suporte à **multihoming** (múltiplos links com ISPs diferentes).

---

## 📍 3. Onde o BGP é usado?

- **ISPs (Provedores de Internet)**: para trocar rotas com outros ISPs e anunciar suas redes públicas.
- **Datacenters e Nuvens**: para redundância e controle de tráfego.
- **Empresas Multinacionais**: com múltiplas filiais e links com diferentes ISPs.
- **Pontos de Troca de Tráfego (IXPs)**: para interconectar redes de forma eficiente.

---

## ✅ 4. Vantagens do BGP

| Vantagem | Descrição |
|---------|-----------|
| 🎯 Controle de Políticas | Você decide qual caminho usar para enviar ou aceitar tráfego. |
| 🔁 Suporte a Multihoming | Redundância com múltiplos ISPs. |
| 🔍 Visibilidade do Caminho | Baseado em ASN Path — você vê todos os ASs por onde o tráfego passa. |
| ⚖️ Balanceamento de Tráfego | Pode distribuir tráfego com base em políticas. |
| 🌍 Escalável | Suporta a quantidade de rotas da Internet global (>1 milhão). |

---

## ❌ 5. Desvantagens do BGP

| Desvantagem | Descrição |
|-------------|-----------|
| 📚 Complexidade | Requer conhecimento profundo para configurar e manter. |
| 🐢 Convergência Lenta | Leva mais tempo para adaptar-se a mudanças de topologia. |
| 🔐 Falta de Segurança Nativa | Pode ser vulnerável a ataques de *prefix hijacking* sem proteções extras (ex.: RPKI). |
| ⚙️ Manutenção Manual | Exige filtros e políticas bem definidas para evitar propagação indevida de rotas. |

---

## ⚙️ 6. Funcionamento Básico

### 6.1 Estabelecimento de Sessão
- Usa **TCP porta 179**.
- Troca mensagens **OPEN**, **KEEPALIVE**, **UPDATE**, **NOTIFICATION**.
- Cada roteador BGP anuncia **prefixos IP** e **atributos** (como o AS_PATH, NEXT_HOP, LOCAL_PREF).

### 6.2 Processo de Escolha de Rota (Best Path Selection)
Ordem dos critérios:
1. **Highest LOCAL_PREF**
2. **Shortest AS_PATH**
3. **Lowest origin type** (IGP < EGP < Incomplete)
4. **Lowest MED (Multi Exit Discriminator)**
5. **eBGP > iBGP**
6. **Lowest IGP metric to next hop**
7. **Oldest route**
8. **Lowest BGP Router ID**

---

## 🛠️ 7. Exemplo Prático (Huawei)

### 7.1 Configuração BGP (Huawei NE9000):

```
[Huawei] bgp 65001
[Huawei-bgp] peer 192.0.2.2 as-number 65002
[Huawei-bgp] network 203.0.113.0 255.255.255.0
```

### 7.2 Topologia Básica:

```
[AS 65001] ---- eBGP ---- [AS 65002]
   R1                     R2
```

R1 anuncia a rede `203.0.113.0/24` para R2.

---

## 🧱 8. Componentes e Atributos Importantes

- **AS_PATH**: Caminho de ASs até o destino (usado para evitar loops).
- **NEXT_HOP**: IP do próximo salto.
- **LOCAL_PREF**: Preferência de rota dentro do AS (maior = melhor).
- **MED**: Indica preferência entre múltiplos caminhos recebidos de outro AS (menor = melhor).
- **COMMUNITIES**: Marcadores para aplicar políticas.

---

## 🔐 9. Segurança no BGP

- **RPKI (Resource Public Key Infrastructure)**: valida se um prefixo pode ser anunciado por um AS.
- **Prefix Filtering**: só aceitar prefixos esperados.
- **Max-Prefix Limit**: limite de prefixos por sessão.
- **TTL Security Hack (GTSM)**: protege contra spoofing.
- **BGP Session Password (MD5)**

---

## 🧪 10. Testes de Rotas BGP (Huawei)

### Verificar sessão BGP:
```
[Huawei] display bgp peer
```

### Verificar tabela de rotas BGP:
```
[Huawei] display bgp routing-table
```

### Verificar rota específica:
```
[Huawei] display bgp routing-table 8.8.8.0
```

---

## 🧩 11. BGP em Ambientes Reais

- Google, Amazon, Facebook — todos usam BGP massivamente.
- Redes de universidades e provedores locais.
- Em **IX.br (Ponto de troca de tráfego no Brasil)**, o BGP é o protocolo essencial.

---

## 📚 12. Recursos para Aprender

- **RFC 4271** – Especificação oficial do BGPv4.
- **BGP4 - Inter-Domain Routing in the Internet** (Livro - Halabi)
- **Labs práticos**: GNS3, EVE-NG, Huawei eNSP, ou roteadores reais (ex: Mikrotik, Huawei, Cisco).
- Sites como [bgp.he.net](https://bgp.he.net/) e [Looking Glass](https://www.nanog.org/resources/looking-glass/) para ver o BGP em ação.

---

## 🔌 13. BGP no Mikrotik

Para essa documentação iremos usar a topologia R1 <> R2

<p align="center">
  <img src="images/1.png"/>
</p>

- Para fecharmos uma seção BGP, será necessário um IP e um ASN, nesse template de configuração iremos usar uma VLAN.

Informações necessárias para fecharmos nosa seção:

R1:
- VLAN: 10
- IP VLAN: 10.200.200.1/30
- ASN: 65001

R2:
- VLAN: 10
- IP VLAN: 10.200.200.2/30
- ASN: 65002

Note-se que o IP e o ASN usado são privados, em um ambiente real ambos ASNs e IPs usados serão públicos.

### Fechando seção

1. Crie a vlan 10 adicionando ela a interface ether2 no roteador R1

<p align="center">
  <img src="images/2.png"/>
</p>

2. Adicione IP a vlan

<p align="center">
  <img src="images/3.png"/>
</p>

3. Faço o mesmo com o roteador R2

<p align="center">
  <img src="images/4.png"/>
</p>

4. Verifique se ambos os roteadores tem ping entre si

<p align="center">
  <img src="images/5.png"/>
</p>

Nossos roteadores já estão se comunincando, agora iremos fechar a seção BGP (Routing > BGP)

R1:

<p align="center">
  <img src="images/6.png"/>
</p>

R2:

<p align="center">
  <img src="images/7.png"/>
</p>

- Após configurar nosso ASN e IP em casa roteador basta ir na aba "Sessions" para saber se conectou e qual estado da conexão

<p align="center">
  <img src="images/8.png"/>
</p>

Veja que nossa conexão está como "Estabelecida", o que significa que deu certo!

### Enviando rota default

- Veja que na nossa topologia, o Roteador R1 recebe um link, e ele tem ping para internet

<p align="center">
  <img src="images/9.png"/>
</p>

- Diferente do nosso R2, que não tem ping para internet pois não tem rota default

<p align="center">
  <img src="images/10.png"/>
</p>

- Na versão 7 do mikrotik temos uma forma muito simples de enviar a rota default, basta ir em (Routing > BGP, Selecionar a conexão > Extra)

<p align="center">
  <img src="images/11.png"/>
</p>

- Ao fazer isso nosso roteador R2 irá receber a rota default e consequentemente ter ping para fora, lembrando que nosso R1 está fazendo NAT

<p align="center">
  <img src="images/12.png"/>
</p>

### Anunciando prefixos

1. Na versão V7 do mikrotik para anunciarmos um prefixo iremos precisar criar uma address-list com os blocos que iremos anunciar

<p align="center">
  <img src="images/13.png"/>
</p>

2. Logos após iremos adicionar a nossa tabela de roteamento para que seja possível anunciar o bloco

<p align="center">
  <img src="images/14.png"/>
</p>

3. Por fim iremos informar qual bloco será anunciado

<p align="center">
  <img src="images/15.png"/>
</p>

4. Podemos conferir no roteador R2 se recebos o bloco

<p align="center">
  <img src="images/16.png"/>
</p>

5. Caso queria anuciar um bloco do roteador R2 para o roteador 1 basta seguir o mesma lógica: Criar a address list, adiconala a tabela de roteamento em blackhole e dizer na aba de filtros qual bloco será anunciado.

### Filtros de roteamento

<p align="center">
  <img src="images/0.png" width="400px"/>
</p>
