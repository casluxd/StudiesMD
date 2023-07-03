O Network Mapper ( `Nmap`) é uma ferramenta de análise de rede e auditoria de segurança de software livre escrita em C, C++, Python e Lua. Ele é projetado para escanear redes e identificar quais hosts estão disponíveis na rede usando pacotes brutos, serviços e aplicativos, incluindo o nome e a versão, sempre que possível. Ele também pode identificar os sistemas operacionais e as versões desses hosts. Além de outros recursos, o Nmap também oferece recursos de varredura que podem determinar se os filtros de pacotes, firewalls ou sistemas de detecção de intrusão (IDS) estão configurados conforme necessário.

## Casos de uso

A ferramenta é uma das mais utilizadas por administradores de rede e especialistas em segurança de TI. É usado para:
- Auditar os aspectos de segurança das redes
- Simular testes de penetração
- Verifique as configurações e configurações de firewall e IDS
- Tipos de conexões possíveis
- Mapeamento de rede
- Análise de resposta
- Identificar portas abertas
- Avaliação de vulnerabilidade

## Arquitetura Nmap

O Nmap oferece muitos tipos diferentes de varreduras que podem ser usadas para obter vários resultados sobre nossos alvos. Basicamente, o Nmap pode ser dividido nas seguintes técnicas de varredura:
- Descoberta de host
- Escaneamento de porta
- Enumeração e detecção de serviço
- Detecção de sistema operacional
- Interação programável com o serviço de destino (Nmap Scripting Engine)

## Sintaxe

A sintaxe do Nmap é bastante simples e se parece com isso:

```shell-session
casluxd@htb[/htb]$ nmap <scan types> <options> <target>
```

## Técnicas de digitalização

O Nmap oferece muitas técnicas de varredura diferentes, fazendo diferentes tipos de conexões e usando pacotes estruturados de maneira diferente para enviar. Aqui podemos ver todas as técnicas de escaneamento que o Nmap oferece:

```shell-session
casluxd@htb[/htb]$ nmap --help

<SNIP>
SCAN TECHNIQUES:
  -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
  -sU: UDP Scan
  -sN/sF/sX: TCP Null, FIN, and Xmas scans
  --scanflags <flags>: Customize TCP scan flags
  -sI <zombie host[:probeport]>: Idle scan
  -sY/sZ: SCTP INIT/COOKIE-ECHO scans
  -sO: IP protocol scan
  -b <FTP relay host>: FTP bounce scan
<SNIP>
```

Por exemplo, a varredura TCP-SYN ( `-sS`) é uma das configurações padrão, a menos que tenhamos definido o contrário, e também é um dos métodos de varredura mais populares. Este método de varredura possibilita a varredura de vários milhares de portas por segundo. A varredura TCP-SYN envia um pacote com o sinalizador SYN e, portanto, nunca conclui o handshake triplo, o que resulta no não estabelecimento de uma conexão TCP completa com a porta rastreada.

- Se nosso alvo enviar um pacote `SYN-ACK` marcado de volta para a porta escaneada, o Nmap detecta que a porta é `open`.
- - Se o pacote receber um sinalizador `RST`, é um indicador de que a porta é `closed`.
- Se o Nmap não receber um pacote de volta, ele o exibirá como `filtered`. Dependendo da configuração do firewall, determinados pacotes podem ser descartados ou ignorados pelo firewall.

Vamos dar um exemplo de tal varredura.

```shell-session
casluxd@htb[/htb]$ sudo nmap -sS localhost

Starting Nmap 7.80 ( https://nmap.org ) at 2020-06-11 22:50 UTC
Nmap scan report for localhost (127.0.0.1)
Host is up (0.000010s latency).
Not shown: 996 closed ports
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
5432/tcp open  postgresql
5901/tcp open  vnc-1

Nmap done: 1 IP address (1 host up) scanned in 0.18 seconds
```

Neste exemplo, podemos ver que temos quatro portas TCP diferentes abertas. Na primeira coluna, vemos o número da porta. Então, na segunda coluna, vemos o status do serviço e, em seguida, que tipo de serviço é.
