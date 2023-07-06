As funções `bind` e `getsockname` usam o tipo de dado genérico `struct sockaddr *` para representar um ponteiro para um endereço de socket. Você não pode usar esse tipo de dado efetivamente para interpretar um endereço ou construir um; para isso, você deve usar o tipo de dados apropriado para o namespace do socket.

Portanto, a prática usual é construir um endereço do tipo específico do namespace adequado e, em seguida, lançar um ponteiro para `struct sockaddr *` quando você chamar `bind` ou `getsockname`.

A única informação que você pode obter do tipo de dados `struct sockaddr` é o `address format designator`. Isso informa qual tipo de dados usar para entender o endereço completamente.

Os símbolos nesta seção são definidos no arquivo de cabeçalho `sys/socket.h`

### Data Type: struct sockaddr

O próprio tipo struct sockaddr tem os seguintes membros:
- **`short int sa_family`**: Este é o código para o formato de endereço deste endereço. Ele identifica o formato dos dados que seguem.
- **`chart sa_data[14]`**: Esses são os dados reais do endereço do socket, que dependem do formato. Seu comprimento também depende do formato e pode ser maior que 14. O comprimento 14 de sa_data é essencialmente arbitrário.

Cada formato de endereço tem um nome simbólico que começa com `AF_`. Cada um deles corresponde a um símbolo `PF_` que designa o namespace correspondente. Aqui está uma lista de nomes de formato de endereço.

- AF_LOCAL: Isso designa o formato de endereço que acompanha o namespace local. (PF_LOCAL é o nome mdesse namespace.)
- AF_FILE: Este é um sinônimo para AF_LOCAL, para compatibilidade.
- AF_UNIX: Este é outro sinônimo para AF_LOCAL. Embora AF_LOCAL seja exigido pelo POSIX.1g, AF_UNIX é portável para mais sistemas. AF_UNIX era o nome tradicional derivado do BSD, então mesmo a maioria dos sistemas POSIX o suporta.
- AF_INET: Isso designa o formato de endereço que acompanha o namespace da Internet. (PF_INET é o nome desse namespace).
- AF_INET6: Este é similar ao `AF_INET`, mas se refere ao protocolo IPv6.
- AF_UNSPEC: Isso não designa nenhum formato de endereço específico. Ele é usado apenas em casos raros, como parar limpar o endereço de destino padrão de um socket de datagrama "conectado". Veja [Sending Datagrams](https://www.gnu.org/software/libc/manual/html_node/Sending-Datagrams.html).