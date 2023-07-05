##

Execução = 1
Escrita = 2
Leitura = 4

Ordem de permissão: rwx

## Alterar permissões

Sintaxe:

`chmod [permissões] [arquivo ou diretório]`

rwx
111            1 = ligado / 0 = desligado

--x
001 = 1

-w-
010 = 2

r--
100 = 4

rw-
110 = 6

-wx
011 = 3

rwx
111 = 7

Para realizar as alterações de permissões de um arquivo ou diretório no sistema, é necessário setar as permissões para usuário, grupo e outros.

rwx   rw-   rw-
111    110   110
7        6      6

`chmod 766 [arquivo ou diretório]`

