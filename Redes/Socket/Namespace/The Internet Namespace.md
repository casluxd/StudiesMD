Essa seção descreve os detalhes dos protocolos e convenções de nomeações de sockets usados no namespace da Internet.

Originalmente, o namespace da Internet usava apenas IP versão 4 (IPv4). Com o crescente número de hosts na Internet, foi necessário um novo protocolo com maior endereçamento: o IP versão 6 (IPv6). O IPv6 introduz endereços de 128 bits (o IPv4 tem endereços de 32 bits) e outros recursos e, eventualmente, substituirá o IPv4.

Para criar um socket no namespace da Internet IPv4, use o nome simbólico de PF_INET desse namespace como argumento de namespace para `socket` ou `socketpair`. Para endereços IPv6, você precisa da macro PF_INET6. Essas macros são definidas em `sys/socket.h`.

### Macro: int PF_INET

Isso designa o namespace IPv4 da Internet e a família de protocolos associada.

### Macro: int PF_INET6

Isso designa o namespace IPv6 da Internet e a família de protocolos associada.


Um endereço de socket para o namespace da Internet inclui os seguintes componentes:
- O endereço da máquina à qual você deseja se conectar. Os endereços da Internet podem ser especificados de várias maneiras; eles são discutidos em [Internet Socket Address Formats](https://www.gnu.org/software/libc/manual/html_node/Internet-Address-Formats.html), [Host Addresses](https://www.gnu.org/software/libc/manual/html_node/Host-Addresses.html) e [Host Names](https://www.gnu.org/software/libc/manual/html_node/Host-Names.html).
- Um número de porta para essa máquina: Veja [Internet Ports](https://www.gnu.org/software/libc/manual/html_node/Ports.html).

Você deve garantir que o endereço e o número da porta sejam representados em um formato canônico chamado `network byte order`. Consulte [Byte Order Conversion](https://www.gnu.org/software/libc/manual/html_node/Byte-Order.html) para obter informações sobre isso.

