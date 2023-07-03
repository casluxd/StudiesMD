`Simple Network Management Protocol` ([SNMP](https://datatracker.ietf.org/doc/html/rfc1157)) foi criado para monitorar dispositivos de rede. Além disso, este protocolo também pode ser usado para lidar com tarefas de configuração e alterar as configurações remotamente. O hardware habilitado para SNMP inclui roteadores, comutadores, servidores, dispositivos de IoT e muitos outros dispositivos que também podem ser consultados e controlados usando este protocolo padrão. Portanto, é um protocolo para monitorar e gerenciar dispositivos de rede. Além disso, as tarefas de configuração podem ser tratadas e as configurações podem ser feitas remotamente usando esse padrão. A versão atual é `SNMPv3`, o que aumenta a segurança do SNMP em particular, mas também a complexidade do uso deste protocolo. 

Além da pura troca de informações, o SNMP também transmite comandos de controle usando agentes na porta UDP `161`. O cliente pode definir valores específicos no dispositivo e alterar opções e configurações com esses comandos. Enquanto na comunicação clássica, é sempre o cliente que solicita ativamente informações do servidor, o SNMP também permite o uso dos chamados `traps` sobre a porta UDP `162`. Estes são pacotes de dados enviados do servidor SNMP para o cliente sem serem solicitados explicitamente. Se um dispositivo estiver configurado de acordo, uma armadilha SNMP será enviada ao cliente assim que um evento específico ocorrer no lado do servidor.

Para que o cliente e o servidor SNMP troquem os respectivos valores, os objetos SNMP disponíveis devem ter endereços exclusivos conhecidos nos dois lados. Esse mecanismo de endereçamento é um pré-requisito absoluto para a transmissão bem-sucedida de dados e monitoramento de rede usando o SNMP.

#### MIB

Para garantir que o acesso ao SNMP funcione entre fabricantes e com diferentes combinações cliente-servidor, o `Management Information Base` (`MIB`) foi criado. MIB é um formato independente para armazenar informações do dispositivo. Um MIB é um arquivo de texto no qual todos os objetos SNMP consultáveis de um dispositivo são listados em uma hierarquia de árvore padronizada. Ele contém pelo menos um `Object Identifier` (`OID`), que, além do endereço exclusivo e do nome necessários, também fornece informações sobre o tipo, direitos de acesso e uma descrição do respectivo objeto. Os arquivos MIB são gravados no `Abstract Syntax Notation One` (`ASN.1`Formato de texto baseado em ASCII). Os MIBs não contêm dados, mas explicam onde encontrar quais informações e como elas são, quais retornam valores para o OID específico ou qual tipo de dados é usado.

#### OID

Um OID representa um nó em um espaço de nome hierárquico. Uma sequência de números identifica exclusivamente cada nó, permitindo que a posição do nó na árvore seja determinada. Quanto mais longa a cadeia, mais específicas são as informações. Muitos nós na árvore OID não contêm nada, exceto referências às abaixo deles. Os OIDs consistem em números inteiros e geralmente são concatenados por notação de pontos. Podemos procurar muitos MIBs para os OIDs associados no [Registro de Identificador de Objeto](https://www.alvestrand.no/objectid/).

#### SNMPv1

Versão SNMP 1 (`SNMPv1`) é usado para gerenciamento e monitoramento de rede. O SNMPv1 é a primeira versão do protocolo e ainda está em uso em muitas redes pequenas. Ele suporta a recuperação de informações de dispositivos de rede, permite a configuração de dispositivos e fornece armadilhas, que são notificações de eventos. No entanto, o SNMPv1 possui mecanismo `built-in de autenticação`, o que significa que qualquer pessoa que acesse a rede pode ler e modificar dados de rede. Outra falha principal do SNMPv1 é que ele `não suporta criptografia`, significando que todos os dados são enviados em texto sem formatação e podem ser facilmente interceptados.

#### SNMPv2

O SNMPv2 existia em diferentes versões. A versão que ainda existe hoje é `v2c`, e a extensão `c` significa SNMP baseado na comunidade. Em relação à segurança, o SNMPv2 está em pé de igualdade com o SNMPv1 e foi estendido com funções adicionais do SNMP baseado em partes que não estão mais em uso. No entanto, um problema significativo com a execução inicial do protocolo SNMP é que o `community string` que fornece segurança é transmitida apenas em texto sem formatação, o que significa que não possui criptografia embutida.

#### SNMPv3

A segurança aumentou enormemente para `SNMPv3` por recursos de segurança, como `authentication` usando nome de usuário e senha e transmissão `encryption` ( via `pre-shared key`) dos dados. No entanto, a complexidade também aumenta na mesma extensão, com significativamente mais opções de configuração do que `v2c`.

#### Community Strings

As cadeias de caracteres da comunidade podem ser vistas como senhas usadas para determinar se as informações solicitadas podem ser visualizadas ou não. É importante observar que muitas organizações ainda estão usando `SNMPv2`, como a transição para `SNMPv3` pode ser muito complexo, mas os serviços ainda precisam permanecer ativos. Isso causa muita preocupação a muitos administradores e cria alguns problemas que eles desejam evitar. A falta de conhecimento sobre como as informações podem ser obtidas e como nós, como atacantes, as utilizamos, fazem com que a abordagem dos administradores pareça inexplicável. Ao mesmo tempo, a falta de criptografia dos dados enviados também é um problema. Porque toda vez que as cadeias da comunidade são enviadas pela rede, elas podem ser interceptadas e lidas.

## Configuração padrão

A configuração padrão do daemon SNMP define as configurações básicas do serviço, que incluem os endereços IP, portas, MIB, OIDs, autenticação e cadeias de comunidade.

#### Configuração do SNMP Daemon

```shell-session
casluxd@htb[/htb]$ cat /etc/snmp/snmpd.conf | grep -v "#" | sed -r '/^\s*$/d'

sysLocation    Sitting on the Dock of the Bay
sysContact     Me <me@example.org>
sysServices    72
master  agentx
agentaddress  127.0.0.1,[::1]
view   systemonly  included   .1.3.6.1.2.1.1
view   systemonly  included   .1.3.6.1.2.1.25.1
rocommunity  public default -V systemonly
rocommunity6 public default -V systemonly
rouser authPrivUser authpriv -V systemonly
```

A configuração deste serviço também pode ser alterada de várias maneiras. Portanto, recomendamos a configuração de uma VM para instalar e configurar o servidor SNMP. Todas as configurações que podem ser feitas para o daemon SNMP são definidas e descritas no [página de manual](http://www.net-snmp.org/docs/man/snmpd.conf.html).

## Configurações perigosas

Algumas configurações perigosas que o administrador pode fazer com o SNMP são:

|**Configurações**|**Descrição**|
|---|---|
|`rwuser noauth`|Fornece acesso à árvore OID completa sem autenticação.|
|`rwcommunity <community string> <IPv4 address>`|Fornece acesso à árvore completa de OID, independentemente de onde os pedidos foram enviados.|
|`rwcommunity6 <community string> <IPv6 address>`|Mesmo acesso que com `rwcommunity` com a diferença de usar IPv6.|

## Footprinting o serviço

Para fazer footprinting do SNMP, podemos usar ferramentas como `snmpwalk`, `onesixtyone`, e `braa`. `Snmpwalk` é usado para consultar os OIDs com suas informações. `Onesixtyone` pode ser usado para bruteforce os nomes das cadeias da comunidade, pois elas podem ser nomeadas arbitrariamente pelo administrador. Como essas cadeias de comunidade podem estar vinculadas a qualquer fonte, a identificação das cadeias de comunidade existentes pode levar algum tempo.

#### SNMPwalk

```shell-session
casluxd@htb[/htb]$ snmpwalk -v2c -c public 10.129.14.128

iso.3.6.1.2.1.1.1.0 = STRING: "Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64"
iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
iso.3.6.1.2.1.1.3.0 = Timeticks: (5134) 0:00:51.34
iso.3.6.1.2.1.1.4.0 = STRING: "mrb3n@inlanefreight.htb"
iso.3.6.1.2.1.1.5.0 = STRING: "htb"
iso.3.6.1.2.1.1.6.0 = STRING: "Sitting on the Dock of the Bay"
iso.3.6.1.2.1.1.7.0 = INTEGER: 72
iso.3.6.1.2.1.1.8.0 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.2.1 = OID: iso.3.6.1.6.3.10.3.1.1
iso.3.6.1.2.1.1.9.1.2.2 = OID: iso.3.6.1.6.3.11.3.1.1
iso.3.6.1.2.1.1.9.1.2.3 = OID: iso.3.6.1.6.3.15.2.1.1
iso.3.6.1.2.1.1.9.1.2.4 = OID: iso.3.6.1.6.3.1
iso.3.6.1.2.1.1.9.1.2.5 = OID: iso.3.6.1.6.3.16.2.2.1
iso.3.6.1.2.1.1.9.1.2.6 = OID: iso.3.6.1.2.1.49
iso.3.6.1.2.1.1.9.1.2.7 = OID: iso.3.6.1.2.1.4
iso.3.6.1.2.1.1.9.1.2.8 = OID: iso.3.6.1.2.1.50
iso.3.6.1.2.1.1.9.1.2.9 = OID: iso.3.6.1.6.3.13.3.1.3
iso.3.6.1.2.1.1.9.1.2.10 = OID: iso.3.6.1.2.1.92
iso.3.6.1.2.1.1.9.1.3.1 = STRING: "The SNMP Management Architecture MIB."
iso.3.6.1.2.1.1.9.1.3.2 = STRING: "The MIB for Message Processing and Dispatching."
iso.3.6.1.2.1.1.9.1.3.3 = STRING: "The management information definitions for the SNMP User-based Security Model."
iso.3.6.1.2.1.1.9.1.3.4 = STRING: "The MIB module for SNMPv2 entities"
iso.3.6.1.2.1.1.9.1.3.5 = STRING: "View-based Access Control Model for SNMP."
iso.3.6.1.2.1.1.9.1.3.6 = STRING: "The MIB module for managing TCP implementations"
iso.3.6.1.2.1.1.9.1.3.7 = STRING: "The MIB module for managing IP and ICMP implementations"
iso.3.6.1.2.1.1.9.1.3.8 = STRING: "The MIB module for managing UDP implementations"
iso.3.6.1.2.1.1.9.1.3.9 = STRING: "The MIB modules for managing SNMP Notification, plus filtering."
iso.3.6.1.2.1.1.9.1.3.10 = STRING: "The MIB module for logging SNMP Notifications."
iso.3.6.1.2.1.1.9.1.4.1 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.2 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.3 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.4 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.5 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.6 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.7 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.8 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.9 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.1.9.1.4.10 = Timeticks: (0) 0:00:00.00
iso.3.6.1.2.1.25.1.1.0 = Timeticks: (3676678) 10:12:46.78
iso.3.6.1.2.1.25.1.2.0 = Hex-STRING: 07 E5 09 14 0E 2B 2D 00 2B 02 00 
iso.3.6.1.2.1.25.1.3.0 = INTEGER: 393216
iso.3.6.1.2.1.25.1.4.0 = STRING: "BOOT_IMAGE=/boot/vmlinuz-5.11.0-34-generic root=UUID=9a6a5c52-f92a-42ea-8ddf-940d7e0f4223 ro quiet splash"
iso.3.6.1.2.1.25.1.5.0 = Gauge32: 3
iso.3.6.1.2.1.25.1.6.0 = Gauge32: 411
iso.3.6.1.2.1.25.1.7.0 = INTEGER: 0
iso.3.6.1.2.1.25.1.7.0 = No more variables left in this MIB View (It is past the end of the MIB tree)

...SNIP...

iso.3.6.1.2.1.25.6.3.1.2.1232 = STRING: "printer-driver-sag-gdi_0.1-7_all"
iso.3.6.1.2.1.25.6.3.1.2.1233 = STRING: "printer-driver-splix_2.0.0+svn315-7fakesync1build1_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1234 = STRING: "procps_2:3.3.16-1ubuntu2.3_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1235 = STRING: "proftpd-basic_1.3.6c-2_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1236 = STRING: "proftpd-doc_1.3.6c-2_all"
iso.3.6.1.2.1.25.6.3.1.2.1237 = STRING: "psmisc_23.3-1_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1238 = STRING: "publicsuffix_20200303.0012-1_all"
iso.3.6.1.2.1.25.6.3.1.2.1239 = STRING: "pulseaudio_1:13.99.1-1ubuntu3.12_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1240 = STRING: "pulseaudio-module-bluetooth_1:13.99.1-1ubuntu3.12_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1241 = STRING: "pulseaudio-utils_1:13.99.1-1ubuntu3.12_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1242 = STRING: "python-apt-common_2.0.0ubuntu0.20.04.6_all"
iso.3.6.1.2.1.25.6.3.1.2.1243 = STRING: "python3_3.8.2-0ubuntu2_amd64"
iso.3.6.1.2.1.25.6.3.1.2.1244 = STRING: "python3-acme_1.1.0-1_all"
iso.3.6.1.2.1.25.6.3.1.2.1245 = STRING: "python3-apport_2.20.11-0ubuntu27.21_all"
iso.3.6.1.2.1.25.6.3.1.2.1246 = STRING: "python3-apt_2.0.0ubuntu0.20.04.6_amd64" 

...SNIP...
```

No caso de uma configuração incorreta, obteríamos aproximadamente os mesmos resultados de `snmpwalk` como mostrado acima. Depois de conhecermos a sequência da comunidade e o serviço SNMP que não requer autenticação ( versões 1, 2c ), podemos consultar informações internas do sistema, como no exemplo anterior.

Aqui reconhecemos alguns pacotes Python que foram instalados no sistema. Se não conhecermos a sequência da comunidade, podemos usar `onesixtyone` e `SecLists` listas de palavras para identificar essas cadeias de comunidade.

#### onesixtyone

```shell-session
casluxd@htb[/htb]$ sudo apt install onesixtyone
casluxd@htb[/htb]$ onesixtyone -c /opt/useful/SecLists/Discovery/SNMP/snmp.txt 10.129.14.128

Scanning 1 hosts, 3220 communities
10.129.14.128 [public] Linux htb 5.11.0-37-generic #41~20.04.2-Ubuntu SMP Fri Sep 24 09:06:38 UTC 2021 x86_64
```

Freqüentemente, quando certas cadeias de comunidade estão vinculadas a endereços IP específicos, elas são nomeadas com o nome do host do host, e às vezes até símbolos são adicionados a esses nomes para torná-los mais desafiadores de identificar. No entanto, se imaginarmos uma extensa rede com mais de 100 servidores diferentes gerenciados usando o SNMP, os rótulos, nesse caso, terão algum padrão para eles. Portanto, podemos usar regras diferentes para adivinhá-las. Podemos usar a ferramenta [triturar](https://secf00tprint.github.io/blog/passwords/crunch/advanced/en) para criar listas de palavras personalizadas. Criar listas de palavras personalizadas não é uma parte essencial deste módulo, mas mais detalhes podem ser encontrados no módulo [Quebrando senhas com Hashcat](https://academy.hackthebox.com/course/preview/cracking-passwords-with-hashcat).

Depois de conhecermos uma sequência de comunidade, podemos usá-la com [braa](https://github.com/mteg/braa) forçar brutalmente os OIDs individuais e enumerar as informações por trás deles.

```shell-session
casluxd@htb[/htb]$ sudo apt install braa
casluxd@htb[/htb]$ braa <community string>@<IP>:.1.3.6.*   # Syntax
casluxd@htb[/htb]$ braa public@10.129.14.128:.1.3.6.*

10.129.14.128:20ms:.1.3.6.1.2.1.1.1.0:Linux htb 5.11.0-34-generic #36~20.04.1-Ubuntu SMP Fri Aug 27 08:06:32 UTC 2021 x86_64
10.129.14.128:20ms:.1.3.6.1.2.1.1.2.0:.1.3.6.1.4.1.8072.3.2.10
10.129.14.128:20ms:.1.3.6.1.2.1.1.3.0:548
10.129.14.128:20ms:.1.3.6.1.2.1.1.4.0:mrb3n@inlanefreight.htb
10.129.14.128:20ms:.1.3.6.1.2.1.1.5.0:htb
10.129.14.128:20ms:.1.3.6.1.2.1.1.6.0:US
10.129.14.128:20ms:.1.3.6.1.2.1.1.7.0:78
...SNIP...
```

Mais uma vez, gostaríamos de salientar que a configuração independente do serviço SNMP nos trará uma grande variedade de experiências diferentes que nenhum tutorial pode substituir. Portanto, é altamente recomendável configurar uma VM com SNMP, experimentar com ela e tentar configurações diferentes. O SNMP pode ser um benefício para um I.T. administrador de sistemas, bem como uma maldição para analistas e gerentes de segurança.