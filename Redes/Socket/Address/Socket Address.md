O nome de um socket é normalmente chamado de "address". As funções e símbolos para lindar com endereços de socket foram nomeados de forma inconsistente, às vezes usando o termo "name" e as vezes usando o termo "address". Você pode considerar esses termos como sinônimos no que diz respeito aos sockets.

Um socket recém-criado com a função socket não tem endereço. Outros processos podem localizá-lo para comunicação apenas se você fornecer um endereço. Chamamos isso de "binding" o endereço ao socket, e a maneira de fazer isso é com uma função "bind". 

Você só precisa se preocupar com o endereço de um socket se outros processos o encontrarem e começarem a se comunicar com ele. Você pode especificar um endereço para outros sockets, mas isso geralmente é inútil; na primeira vez que você enviar dados de um socket ou usá-lo para iniciar uma conexão, o sistema atribuirá um endereço automaticamente, caso você não tenha especificado um.

Ocasionalmente, um cliente precisa especificar um endereço porque o servidor discrimina com base no endereço; por exemplo, os protocolos rsh e rlogin verificam o endereço do socket do cliente e apenas ignoram a verificação da senha se for menor que IPPORT_RESERVED.

Os detalhes dos endereços de socket variam dependendo de qual namespace você está usando. Veja  [The Local Namespace](https://www.gnu.org/software/libc/manual/html_node/Local-Namespace.html) ou [The Internet Namespace](https://www.gnu.org/software/libc/manual/html_node/Internet-Namespace.html), para informações específicas.

Independentemente do namespace, você usa as mesmas funções bind e getsockname para definir e examinar o endereço de um socket. Essas funções usam um tipo de dados falso, `struct sockaddr *`, para aceitar o endereço. Na prática, o endereço reside em uma estrutura de algum outro tipo de dados apropriado para o formato de endereço que você está usando, mas você lança seu endereço para `struct sockaddr *` quando o passa para o `bind`.

