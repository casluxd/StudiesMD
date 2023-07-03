
Se estivéssemos realizando um teste de penetração em um aplicativo da Web desenvolvido internamente ou não encontrássemos nenhum exploit público para um aplicativo da Web público, poderíamos identificar manualmente várias vulnerabilidades. Também podemos descobrir vulnerabilidades causadas por configurações incorretas, mesmo em aplicativos da Web publicamente disponíveis, pois esses tipos de vulnerabilidades não são causados pela versão pública do aplicativo da web, mas por uma configuração incorreta feita pelos desenvolvedores. Os exemplos abaixo são alguns dos tipos de vulnerabilidade mais comuns para aplicativos da web, parte do OWASP Top 10 vulnerabilidades para aplicativos da web.

## Broken Authentication/Access Control

Broken Authentication e Broken Access Control estão entre as vulnerabilidades mais comuns e perigosas para aplicativos da web.

Broken Authentication refere-se a vulnerabilidades que permitem que invasores ignorem as funções de autenticação. Por exemplo, isso pode permitir que um invasor faça login sem ter um conjunto válido de credenciais ou permitir que um usuário normal se torne um administrador sem ter privilégios para isso.

Broken Access Control refere-se a vulnerabilidades que permitem que invasores acessem páginas e recursos aos quais não deveriam ter acesso. Por exemplo, um usuário normal obtendo acesso ao painel de administração.

Por exemplo, o College Management System 1.2 possui uma vulnerabilidade de Auth Bypass simples que nos permite fazer login sem ter uma conta, inserindo o seguinte no campo de e-mail: ' ou 0=0 # e usando qualquer senha com ele.

## Malicious File Upload

Outra maneira comum de obter controle sobre aplicativos da Web é por meio do upload de scripts maliciosos. Se o aplicativo da web tiver um recurso de upload de arquivo e não validar corretamente os arquivos enviados, podemos fazer upload de um script malicioso (ou seja, um script PHP), que nos permitirá executar comandos no servidor remoto.

Embora esta seja uma vulnerabilidade básica, muitos desenvolvedores não estão cientes dessas ameaças, portanto, eles não verificam e validam adequadamente os arquivos carregados. Além disso, alguns desenvolvedores realizam verificações e tentam validar os arquivos carregados, mas essas verificações geralmente podem ser ignoradas, o que ainda nos permitiria carregar scripts maliciosos.

Por exemplo, o WordPress Plugin Responsive Thumbnail Slider 1.0 pode ser explorado para carregar qualquer arquivo arbitrário, incluindo scripts maliciosos, carregando um arquivo com uma extensão dupla (ou seja, shell.php.jpg). Existe até um Módulo Metasploit que nos permite explorar essa vulnerabilidade facilmente.

## Command Injection

Muitos aplicativos da Web executam comandos locais do sistema operacional para executar determinados processos. Por exemplo, um aplicativo da web pode instalar um plug-in de nossa escolha executando um comando do sistema operacional que baixa esse plug-in, usando o nome do plug-in fornecido. Se não for devidamente filtrado e higienizado, os invasores podem injetar outro comando para ser executado juntamente com o comando pretendido originalmente (ou seja, como o nome do plug-in), que permite que eles executem comandos diretamente no servidor de back-end e obtenham controle sobre ele. Esse tipo de vulnerabilidade é chamado de injeção de comando.

Essa vulnerabilidade é generalizada, pois os desenvolvedores podem não higienizar adequadamente a entrada do usuário ou usar testes fracos para fazer isso, permitindo que os invasores ignorem qualquer verificação ou filtragem implementada e executem seus comandos.

Por exemplo, o WordPress Plugin Plainview Activity Monitor 20161228 tem uma vulnerabilidade que permite aos invasores injetar seu comando no valor ip, simplesmente adicionando | COMANDO... após o valor do ip.

## SQL Injection (SQLi)

Outra vulnerabilidade muito comum em aplicativos da Web é uma vulnerabilidade de SQL Injection. Da mesma forma que uma vulnerabilidade de injeção de comando, essa vulnerabilidade pode ocorrer quando o aplicativo da Web executa uma consulta SQL, incluindo um valor obtido da entrada fornecida pelo usuário.

Por exemplo, na seção de banco de dados, vimos um exemplo de como um aplicativo da web usaria a entrada do usuário para pesquisar em uma determinada tabela, com a seguinte linha de código:

```php
$query = "select * from users where name like '%$searchInput%'";
```

Se a entrada do usuário não for devidamente filtrada e validada (como é o caso das injeções de comando), podemos executar outra consulta SQL juntamente com esta consulta, o que pode eventualmente nos permitir assumir o controle sobre o banco de dados e seu servidor de hospedagem.

Por exemplo, o mesmo College Management System 1.2 anterior sofre de uma vulnerabilidade de injeção SQL, na qual podemos executar outra consulta SQL que sempre retorna true, o que significa que autenticamos com sucesso, que nos permite fazer login no aplicativo. Podemos usar a mesma vulnerabilidade para recuperar dados do banco de dados ou até mesmo obter controle sobre o servidor de hospedagem.

Veremos essas vulnerabilidades repetidamente em nossa jornada de aprendizado e avaliações do mundo real. É importante se familiarizar com cada um deles, pois mesmo um entendimento básico de cada um nos dará uma vantagem em qualquer área de segurança da informação.