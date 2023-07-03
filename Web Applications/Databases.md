
Os aplicativos da Web utilizam bancos de dados de back-end para armazenar vários conteúdos e informações relacionados ao aplicativo da Web. Isso pode ser ativos de aplicativos da Web principais, como imagens e arquivos, conteúdo de aplicativos da Web, como postagens e atualizações, ou dados do usuário, como nomes de usuário e senhas. Isso permite que os aplicativos da Web armazenem e recuperem dados de maneira fácil e rápida e habilitem conteúdo dinâmico diferente para cada usuário.

Existem muitos tipos diferentes de bancos de dados, cada um dos quais se adapta a um determinado tipo de uso. A maioria dos desenvolvedores procura por certas características em um banco de dados, como velocidade no armazenamento e recuperação de dados, tamanho ao armazenar grandes quantidades de dados, escalabilidade à medida que o aplicativo da web cresce e custo.

### Relational (SQL)

Os bancos de dados relacionais (SQL) armazenam seus dados em tabelas, linhas e colunas. Cada tabela pode ter chaves exclusivas, que podem vincular tabelas e criar relacionamentos entre tabelas. Por exemplo, podemos ter uma tabela de usuários em um banco de dados relacional contendo colunas como id, username, first_name, last_name e assim por diante. O id pode ser usado como a chave da tabela. Outra tabela, postagens, pode conter postagens feitas por todos os usuários, com colunas como id, user_id, data, conteúdo e assim por diante.

![[Pasted image 20230612144426.png]]

Podemos vincular o id da tabela de usuários ao user_id na tabela de postagens para recuperar facilmente os detalhes do usuário para cada postagem, sem precisar armazenar todos os detalhes do usuário em cada postagem.

Uma tabela pode ter mais de uma chave, pois outra coluna pode ser usada como chave para vincular a outra tabela. Por exemplo, a coluna id pode ser usada como uma chave para vincular a tabela de posts a outra tabela contendo comentários, cada um dos quais pertence a um determinado post, e assim por diante.

`A relação entre tabelas dentro de um banco de dados é chamada de Esquema.`

Desta forma, usando bancos de dados relacionais, torna-se muito rápido e fácil recuperar todos os dados sobre um determinado elemento de todos os bancos de dados. Por exemplo, podemos recuperar todos os detalhes vinculados a um determinado usuário de todas as tabelas com uma única consulta. Isso torna os bancos de dados relacionais muito rápidos e confiáveis ​​para grandes conjuntos de dados com estrutura e design claros. Os bancos de dados também tornam o gerenciamento de dados muito eficiente.

Alguns dos bancos de dados relacionais mais comuns incluem:
- MySQL: O banco de dados mais usado na Internet. É um banco de dados de código aberto e pode ser usado de forma totalmente gratuita
- MSSQL: Implementação da Microsoft de um banco de dados relacional. Amplamente utilizado com Windows Servers e servidores web IIS
- Oracle: Um banco de dados muito confiável para grandes empresas e é frequentemente atualizado com soluções inovadoras de banco de dados para torná-lo mais rápido e confiável. Pode ser caro, mesmo para grandes empresas
- PostgreSQL: Outro banco de dados relacional gratuito e de código aberto. Ele foi projetado para ser facilmente extensível, permitindo adicionar novos recursos avançados sem a necessidade de uma grande alteração no design inicial do banco de dados

Outros bancos de dados SQL comuns incluem: SQLite, MariaDB, Amazon Aurora e Azure SQL.

### Non-relational (noSQL)

Um banco de dados não relacional não usa tabelas, linhas, colunas, chaves primárias, relacionamentos ou esquemas. Em vez disso, um banco de dados NoSQL armazena dados usando vários modelos de armazenamento, dependendo do tipo de dados armazenados.

Devido à falta de uma estrutura definida para o banco de dados, os bancos de dados NoSQL são muito escaláveis ​​e flexíveis. Ao lidar com conjuntos de dados que não são muito bem definidos e estruturados, um banco de dados NoSQL seria a melhor escolha para armazenar nossos dados.

Existem 4 modelos de armazenamento comuns para bancos de dados NoSQL:
- Key-Value
- Document-based
- Wide-Column
- Graph

Cada um dos modelos acima tem uma maneira diferente de armazenar dados. Por exemplo, o modelo Key-Value geralmente armazena dados em JSON ou XML, e possui uma chave para cada par, armazenando todos os seus dados como seu valor:

![[Pasted image 20230612154609.png]]

```json
{
  "100001": {
    "date": "01-01-2021",
    "content": "Welcome to this web application."
  },
  "100002": {
    "date": "02-01-2021",
    "content": "This is the first post on this web app."
  },
  "100003": {
    "date": "02-01-2021",
    "content": "Reminder: Tomorrow is the ..."
  }
}
```

Parece semelhante a um par dicionário/mapa/chave-valor em linguagens como Python ou PHP 'i.e. {'key':'value'}', onde a chave geralmente é uma string, o valor pode ser uma string, dicionário ou qualquer objeto de classe.

O modelo baseado em documento armazena dados em objetos JSON complexos e cada objeto possui determinados metadados enquanto armazena o restante dos dados de maneira semelhante ao modelo de valor-chave.

Alguns dos bancos de dados NoSQL mais comuns incluem:
- MongoDB: O banco de dados NoSQL mais comum. É gratuito e de código aberto, usa o modelo baseado em documentos e armazena dados em objetos JSON
- ElasticSearch: Outro banco de dados NoSQL gratuito e de código aberto. Ele é otimizado para armazenar e analisar grandes conjuntos de dados. Como o próprio nome sugere, a busca de dados dentro deste banco de dados é muito rápida e eficiente
- Apache Cassandra: Também gratuito e de código aberto. É muito escalável e é otimizado para lidar com valores defeituosos

Outros bancos de dados NoSQL comuns incluem: Redis, Neo4j, CouchDB e Amazon DynamoDB.

### Use in Web Applications

A maioria das linguagens e estruturas modernas de desenvolvimento da Web facilitam a integração, o armazenamento e a recuperação de dados de vários tipos de banco de dados. Mas primeiro, o banco de dados deve ser instalado e configurado no servidor back-end e, uma vez instalado e funcionando, os aplicativos da web podem começar a utilizá-lo para armazenar e recuperar dados.

Por exemplo, dentro de um aplicativo da web PHP, uma vez que o MySQL esteja funcionando, podemos nos conectar ao servidor de banco de dados com:

```php
$conn = new mysqli("localhost", "user", "pass");
```

```php
$sql = "CREATE DATABASE database1";
$conn->query($sql)
```

Depois disso, podemos nos conectar ao nosso novo banco de dados e começar a usar o banco de dados MySQL através da sintaxe do MySQL, diretamente no PHP, da seguinte maneira:

```php
$conn = new mysqli("localhost", "user", "pass", "database1");
$query = "select * from table_1";
$result = $conn->query($query);
```

Os aplicativos da Web geralmente usam a entrada do usuário ao recuperar dados. Por exemplo, quando um usuário usa a função de pesquisa para pesquisar outros usuários, sua entrada de pesquisa é passada para o aplicativo da web, que usa a entrada para pesquisar no(s) banco(s) de dados.

```php
$searchInput =  $_POST['findUser'];
$query = "select * from users where name like '%$searchInput%'";
$result = $conn->query($query);
```

Por fim, o aplicativo da Web envia o resultado de volta ao usuário:

```php
while($row = $result->fetch_assoc() ){
	echo $row["name"]."<br>";
}
```

Este exemplo básico nos mostra como é fácil utilizar bancos de dados. No entanto, se não for codificado com segurança, o código do banco de dados pode levar a uma variedade de problemas, como vulnerabilidades de SQL Injection.

