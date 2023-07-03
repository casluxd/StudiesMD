
1. Read (Leitura): Permite que o usuário visualize o conteúdo de um arquivo ou diretório.
2. Write (Escrita): Permite que o usuário faça alterações no conteúdo de um arquivo ou diretório, como adicionar, modificar ou excluir arquivos.
3. Modify (Modificar): Combina as permissões de leitura e escrita. Permite que o usuário leia e modifique o conteúdo de um arquivo ou diretório.
4. Execute (Execução): Permite que o usuário execute um arquivo executável ou navegue em um diretório.
5. Delete (Exclusão): Permite que o usuário exclua um arquivo ou diretório.
6. Full control (Controle total): Permite que o usuário tenha todas as permissões disponíveis, incluindo leitura, escrita, modificação, execução e exclusão.+
7. List folder contents (Listar o conteúdo da pasta): Permite que o usuário veja o conteúdo de um diretório, mas não acesse ou modifique os arquivos dentro dele.
8. Read & execute (Leitura e execução): Combina as permissões de leitura e execução.
9. Read attributes (Ler atributos): Permite que o usuário leia os atributos de um arquivo ou diretório, como atributos de arquivo somente leitura ou oculto.
10. Read extended attributes (Ler atributos estendidos): Permite que o usuário leia atributos estendidos de um arquivo ou diretório.
11. Write attributes (Escrever atributos): Permite que o usuário altere os atributos de um arquivo ou diretório.
12. Write extended attributes (Escrever atributos estendidos): Permite que o usuário escreva atributos estendidos em um arquivo ou diretório.
13. (I) - Herdar permissões somente do pai (Inherit): Esse modificador indica que as permissões definidas para o diretório pai devem ser herdadas pelos arquivos ou subdiretórios contidos nele. Quando aplicado a um diretório, as permissões definidas para ele são herdadas pelos objetos contidos nele, mas não pelos objetos criados posteriormente.
14. (CI) - Herdar permissões do pai e aplicar a objetos filhos (Container Inherit): Esse modificador indica que as permissões definidas para o diretório pai devem ser herdadas pelos objetos contidos nele, bem como pelos objetos que serão criados posteriormente dentro do diretório.
15. (OI) - Herdar permissões do pai e aplicar a objetos filhos somente para arquivos (Object Inherit): Esse modificador indica que as permissões definidas para o diretório pai devem ser herdadas pelos objetos contidos nele, mas não pelos objetos que serão criados posteriormente dentro do diretório. No entanto, essa herança é aplicada somente a arquivos, não a subdiretórios.
16. (F) - Aplicar permissões a objetos filho existentes (Force): Esse modificador é usado para forçar a aplicação das permissões especificadas a objetos filhos existentes, mesmo que a herança de permissões esteja desabilitada. Ele substitui as permissões existentes pelos novos valores especificados.