Uma biblioteca compartilhada é um arquivo que contém inúmeras funções, métodos que podem ser compartilhados e usados por outros aplicativos no sistema. O gerenciamento é de extrema importância, pois uma biblioteca faltante ou corrompida pode fazer com que os aplicativos não funcionem de forma correta.

- Comando ldd: Exibe as bibliotecas compartilhadas requeridas por cada um dos programas listados na sua linha de comando, trazendo o nome da biblioteca e o local onde se esperá encontrá-la.

`ldd [programas]` - Imprime as dependências de bibliotecas compartilhadas.
`ldd --version` - Mostra a versão do ldd

## Como funciona o mecanismo de vinculação das bibliotecas - ld.so

Os programas executáveis dinamicamente vinculados são exanimados, no momento da execução, pelo vinculador dinâmico de objetos compartilhados ld.so. 
Ele procura por dependências no executável que está sendo carregado e tenta satisfazer vínculos a bibliotecas compartilhadas de sistema que não estejam resolvidos. Se ele não encontrar uma biblioteca, o programa não poderá rodar.

O ld.so pode procurar pelos diretórios que contém bibliotecas na variável `LD_LIBRARY_PATH` ou as bibliotecas são procuradas em um índice de nomes e localizações de bibliotecas (binários) chamado `/etc/ld.so.cache`

Ao adicionar novas entradas ao cache: adicione o diretório que contém as bibliotecas ao arquivo `/etc/ld.so.conf` e atualize o cache com o comando `ldconfig`.