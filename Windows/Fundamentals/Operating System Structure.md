
Nos sistemas operacionais Windows, o diretório root é <drive_letter>:\ (geralmente a unidade C). O diretório root (também conhecido como partição de inicialização) é onde o sistema operacional está instalado. Outras unidades físicas e virtuais recebem outras letras, por exemplo, Data (E:). A estrutura de diretórios da partição de inicialização é a seguinte:

- Perflogs: Pode conter logs de desempenho do Windows, mas está vazio por padrão.
- Program Files: Em sistemas de 32 bits, todos os programas de 16 e 32 bits são instalados aqui. Em sistemas de 64 bits, apenas programas de 64 bits são instalados aqui.
- Program Files (x86): Programas de 32 bits e 16 bits são instalados aqui nas edições de 64 bits do Windows.
- ProgramData: Esta é uma pasta oculta que contém dados essenciais para a execução de determinados programas instalados. Esses dados são acessíveis pelo programa, independentemente do usuário que os esteja executando.
- Users: Esta pasta contém perfis de usuário para cada usuário que efetua logon no sistema e contém as duas pastas Public e Default.
- Public: Esta pasta destina-se a usuários de computador para compartilhar arquivos e é acessível a todos os usuários por padrão. Esta pasta é compartilhada pela rede por padrão, mas requer uma conta de rede válida para acessar.
- AppData: Os dados e as configurações do aplicativo por usuário são armazenados em uma subpasta oculta do usuário (ou seja, cliff.moore\AppData). Cada uma dessas pastas contém três subpastas. A pasta Roaming contém dados independentes da máquina que devem seguir o perfil do usuário, como dicionários personalizados. A pasta Local é específica do próprio computador e nunca é sincronizada na rede. LocalLow é semelhante à pasta Local, mas possui um nível de integridade de dados inferior. Portanto, pode ser usado, por exemplo, por um navegador da Web configurado para o modo protegido ou seguro.
- Windows: A maioria dos arquivos necessários para o sistema operacional Windows está contida aqui.
- System, System32, SysWOW64: Contém todas as DLLs necessárias para os principais recursos do Windows e da API do Windows. O sistema operacional pesquisa essas pastas sempre que um programa solicita o carregamento de uma DLL sem especificar um caminho absoluto.
- WinSxS: O Windows Component Store contém uma cópia de todos os componentes, atualizações e service packs do Windows.

