
Um conceito importante a ser aprendido em HTML é a Codificação de URL, ou codificação percentual. Para que um navegador exiba corretamente o conteúdo de uma página, ele precisa conhecer o conjunto de caracteres em uso. Em URLs, por exemplo, os navegadores só podem usar a codificação ASCII, que permite apenas caracteres alfanuméricos e alguns caracteres especiais. Portanto, todos os outros caracteres fora do conjunto de caracteres ASCII devem ser codificados em uma URL. A codificação de URL substitui caracteres ASCII não seguros por um símbolo % seguido por dois dígitos hexadecimais.

Por exemplo, o caractere de aspas simples ' é codificado como '%27', que pode ser entendido pelos navegadores como aspas simples. Os URLs não podem conter espaços e substituirão um espaço por um + (sinal de mais) ou %20. Algumas codificações de caracteres comuns são:

- Espaço -> %20
- ! -> %21
- " -> %22
- `#` -> %23
- $ -> %24
- % -> %25
- & -> %26
- ' -> %27
- ( -> %28
- ) -> %29

