No Local Namespace, os endereços de socket são nomes de arquivos. Você pode especificar qualquer nome de arquivo que desejar como endereço de socket, mas deve ter permissão de gravação no diretório que o tem. É comum colocar esses arquivos no diretório `/tmp`.

Uma peculiaridade do namespace local é que o nome só é usado ao abrir a conexão; uma vez aberto, o endereço não é significativo e pode não existir.

Outra peculiaridade é que você não pode se conectar a tal socket de outra máquina, nem mesmo se a outra máquina compartilhar o sistema de arquivos que contém o nome do socket. Você pode ver o socket em uma listagem de diretório, mas a conexão com ele nunca é bem sucedida. Alguns programas tiram proveito disso, como pedir ao cliente para enviar seu próprio ID de processo e usar os IDs de processo para distinguir entre clientes. No entanto, recomendamos que você não use esse método em protocolos que você criar, pois algum dia poderemos permitir conexões de outras máquinas que montam os mesmos sistemas de arquivos. Em vez disso, envie a cada novo cliente um número de identificação, se desejar que ele tenha um.

Depois de fechar um socket no namespace local, você deve excluir o nome do arquivo do sistema de arquivos. Use `unlink` ou `remove` para fazer isso; consulte [Deleting Files](https://www.gnu.org/software/libc/manual/html_node/Deleting-Files.html).

O namespace local suporta apenas um protocolo para qualquer estilo de comunicação; é o protocolo número 0;