Podemos ter ouvido os termos desenvolvimento web front-end e back-end, ou o termo desenvolvimento web Full Stack, que se refere ao desenvolvimento web front-end e back-end. Esses termos estão se tornando sinônimos de desenvolvimento de aplicativos da Web, pois compreendem a maior parte do ciclo de desenvolvimento da Web. No entanto, esses termos são muito diferentes uns dos outros, pois cada um se refere a um lado do aplicativo da web e cada um funciona e se comunica em diferentes áreas.

## Backend

O back-end de um aplicativo da web conduz todas as principais funcionalidades do aplicativo da web, todas executadas no servidor de back-end, que processa tudo o que é necessário para que o aplicativo da web seja executado corretamente. É a parte que talvez nunca vejamos ou com a qual interagimos diretamente, mas um site é apenas uma coleção de páginas estáticas da Web sem um back-end.
- Backend Servers: O hardware e o sistema operacional que hospeda todos os outros componentes e geralmente são executados em sistemas operacionais como Linux, Windows ou usando contêineres.
- Web Servers: Os servidores da Web lidam com solicitações e conexões HTTP. Alguns exemplos são Apache, NGINX e IIS.
- Databases: Bancos de dados (DBs) armazenam e recuperam os dados do aplicativo da web. Alguns exemplos de bancos de dados relacionais são MySQL, MSSQL, Oracle, PostgreSQL, enquanto exemplos de bancos de dados não relacionais incluem NoSQL e MongoDB.
- Development Frameworks: Os frameworks de desenvolvimento são usadas para desenvolver o aplicativo da Web principal. Algumas estruturas bem conhecidas incluem Laravel (PHP), ASP.NET (C#), Spring (Java), Django (Python) e Express (NodeJS JavaScript).

![[Pasted image 20230609153059.png]]

Também é possível hospedar cada componente do back-end em seu próprio servidor isolado ou em contêineres isolados, utilizando serviços como o Docker. Para manter a separação lógica e mitigar o impacto das vulnerabilidades, diferentes componentes do aplicativo da web, como o banco de dados, pode ser instalado em um contêiner do Docker, enquanto o aplicativo da Web principal é instalado em outro, isolando cada parte de possíveis vulnerabilidades que podem afetar o(s) outro(s) contêiner(es). Também é possível separar cada um em seu servidor dedicado, que pode ser mais intensivo em recursos e demorado para manter. Ainda assim, depende do caso de negócios e do design/funcionalidade do aplicativo da Web em questão.

Algumas das principais tarefas executadas pelos componentes de back-end incluem:
- Desenvolva a lógica principal e os serviços do back-end do aplicativo da web
- Desenvolver o código principal e as funcionalidades da aplicação web
- Desenvolver e manter o banco de dados back-end
- Desenvolver e implementar bibliotecas para serem utilizadas pela aplicação web
- Implementar necessidades técnicas/comerciais para o aplicativo da web
- Implemente as principais APIs para comunicações de componentes de front-end
- Integre servidores remotos e serviços em nuvem no aplicativo da web

### Protegendo Front/Back End

Mesmo que na maioria dos casos não tenhamos acesso ao código back-end para analisar as funções individuais e a estrutura do código, isso não torna o aplicativo invulnerável. Ainda pode ser explorado por vários ataques de injeção, por exemplo.

Suponha que temos uma função de pesquisa em um aplicativo da Web que, por engano, não processa corretamente nossas consultas de pesquisa. Nesse caso, poderíamos usar técnicas específicas para manipular as consultas de forma a obter acesso não autorizado para injeções de SQL de dados específicos do banco de dados ou até mesmo executar comandos do sistema operacional por meio do aplicativo da web, também conhecido como Command Injections.

Quando temos acesso total ao código-fonte dos componentes front-end, podemos realizar uma revisão de código para encontrar vulnerabilidades, o que faz parte do que é conhecido como Whitebox Petesting.

Por outro lado, o código-fonte dos componentes de back-end é armazenado no servidor de back-end, portanto, não temos acesso a ele por padrão, o que nos obriga apenas a realizar o que é conhecido como Blackbox Petesting. No entanto, como veremos, alguns aplicativos da web são de código aberto, o que significa que provavelmente temos acesso ao seu código-fonte. Além disso, algumas vulnerabilidades, como inclusão de arquivo local, podem nos permitir obter o código-fonte do servidor de back-end. Com esse código-fonte em mãos, podemos realizar uma revisão de código nos componentes de back-end para entender melhor como o aplicativo funciona, possivelmente encontrar dados confidenciais no código-fonte (como senhas) e até encontrar vulnerabilidades que seriam difíceis ou impossíveis de encontrar sem acesso ao código-fonte.

Os 20 erros mais comuns que os desenvolvedores web cometem e que são essenciais para nós, como testadores de penetração, são:
1. Permitir que dados inválidos entrem no banco de dados
2. Foco no sistema como um todo
3. Estabelecendo métodos de segurança desenvolvidos pessoalmente
4. Tratar a segurança como seu último passo
5. Desenvolvendo armazenamento de senha de texto simples
6. Criando Senhas Fracas
7. Armazenando dados não criptografados no banco de dados
8. Depender Excessivamente do Lado do Cliente
9. Ser muito otimista
10. Permitir variáveis ​​por meio do nome do caminho da URL
11. Confiar em código de terceiros
12. Codificar contas backdoor
13. Injeções de SQL não verificadas
14. Inclusões de arquivos remotos
15. Tratamento de dados inseguro
16. Falha ao criptografar os dados corretamente
17. Não usar um sistema criptográfico seguro
18. Ignorando a camada 8
19. Revise as ações do usuário
20. Configurações incorretas do firewall de aplicativos da Web

Esses erros levam às 10 principais vulnerabilidades do OWASP para aplicativos da Web, que discutiremos em outros módulos:
1. Broken Access Control
2. Cryptographic Failures
3. Injection
4. Insecure Design
5. Security Misconfiguration
6. Vulnerable and Outdated Components
7. Identification and Authentication Failures
8. Software and Data Integrity Failures
9. Security Logging and Monitoring Failures
10. Server-Side Request Forgery (SSRF)

É importante começar a nos familiarizar com essas falhas e vulnerabilidades, pois elas formam a base para muitos dos problemas que abordaremos em módulos futuros da Web e até mesmo não relacionados à Web. Como pentesters, devemos ter a capacidade de identificar, explorar e explicar com competência essas vulnerabilidades para nossos clientes.