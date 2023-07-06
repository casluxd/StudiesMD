Um endereço de socket no namespace da Internet consiste no endereço de Internet de uma máquina mais um número de porta que distingue os sockets em uma determinada máquina (para um determinado protocolo). Os números de porta variam de 0 a 65535.

Números de portas menores que IPPORT_RESERVED são reservados para servidores padrão, como finger e telnet. Há um banco de dados que rastreia isso e você pode usar a função `getservbyname` para mapear um nome de serviço para um número de porta. Consulte [The Services Database](https://www.gnu.org/software/libc/manual/html_node/Services-Database.html).

Se você escrever um servidor que não seja um dos padrões definidos no banco de dados, deverá escolher um número de porta para ele. Use um número maior que IPPORT_USERRESERVED; esses números são reservados para servidores e nunca serão gerados automaticamente pelo sistema. Evitar conflitos com servidores sendo executados por outros usuários depende de você.

Quando você usa um socket sem especificar seu endereço, o sistema gera um número de porta para ele. Este número está entre IPPORT_RESERVED e IPPORT_USERRESERVED.

Na internet, é realmente legítimo ter dois sockets diferentes com o mesmo número de porta, desde que ambos nunca tentem se conectar com o mesmo endereço de socket (endereço de host mais número de porta). Você não deve duplicar um número de porta, exceto em circunstâncias especiais em que um protocolo de nível superior o exija. Normalmente, o sistema não permite que você faça isso; O `bind` normalmente insiste em números de porta distintos. Para reutilizar um número de porta, você deve definir a opção de socket `SO_REUSEADDR`. Veja [Socket-Level Options](https://www.gnu.org/software/libc/manual/html_node/Socket_002dLevel-Options.html).

Esses macros são definidos no arquivo de cabeçalho `netinet/in.h`

- Macro `int IPPORT_RESERVED`: Números de portas menores que IPPORT_RESERVED são reservadas para uso de super usuário.
- Macro `int IPPORT_USERRESERVED`: Números de portas maior ou igual à `IPPORT_USERRESERVED` são reservados para uso explícito; eles nunca serão alocados automaticamente.