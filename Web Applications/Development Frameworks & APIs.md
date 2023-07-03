
Além dos servidores da web que podem hospedar aplicativos da web em vários idiomas, existem muitas estruturas comuns de desenvolvimento da web que ajudam no desenvolvimento de arquivos e funcionalidades de aplicativos da web principais. Com o aumento da complexidade dos aplicativos da Web, pode ser um desafio criar um aplicativo da Web moderno e sofisticado do zero. Portanto, a maioria dos aplicativos da Web populares é desenvolvida usando estruturas da Web.

Como a maioria dos aplicativos da Web compartilha funcionalidades comuns - como o registro do usuário -, as estruturas de desenvolvimento da Web facilitam a implementação rápida dessa funcionalidade e a vinculam aos componentes de front-end, criar um aplicativo da Web totalmente funcional. Algumas das estruturas de desenvolvimento web mais comuns incluem:

- Laravel (PHP): geralmente usado por startups e empresas menores, pois é poderoso, mas fácil de desenvolver.
- Express (Node.JS): usado por PayPal, Yahoo, Uber, IBM e MySpace.
- Django (Python): usado pelo Google, YouTube, Instagram, Mozilla e Pinterest.
- Rails (Ruby): usado pelo GitHub, Hulu, Twitch, Airbnb e até mesmo pelo Twitter no passado.

## Query Parameters

O método padrão de envio de argumentos específicos para uma página da Web é por meio de parâmetros de solicitação GET e POST. Isso permite que os componentes de front-end especifiquem valores para determinados parâmetros usados ​​na página para que os componentes de back-end os processem e respondam de acordo.

Por exemplo, uma página /search.php receberia um parâmetro de item, que pode ser usado para especificar o item de pesquisa. A passagem de um parâmetro por meio de uma solicitação GET é feita por meio da URL '/search.php?item=apples', enquanto os parâmetros POST são passados ​​por meio de dados POST na parte inferior da solicitação HTTP POST:

```http
POST /search.php HTTP/1.1
...SNIP...

item=apples
```

Os parâmetros de consulta permitem que uma única página receba vários tipos de entrada, cada uma das quais pode ser processada de forma diferente. Para alguns outros cenários, as APIs da Web podem ser muito mais rápidas e eficientes de usar.

## Web APIs

Uma API (Application Programming Interface) é uma interface dentro de um aplicativo que especifica como o aplicativo pode interagir com outros aplicativos. Para aplicativos da Web, é o que permite o acesso remoto à funcionalidade dos componentes de back-end. As APIs não são exclusivas de aplicativos da Web e são usadas para aplicativos de software em geral. As APIs da Web geralmente são acessadas pelo protocolo HTTP e geralmente são manipuladas e traduzidas por meio de servidores da Web.

![[Pasted image 20230612180106.png]]

Um aplicativo da web de clima, por exemplo, pode ter uma determinada API para recuperar o clima atual de uma determinada cidade. Podemos solicitar a URL da API e passar o nome da cidade ou id da cidade, e ela retornaria o clima atual em um objeto JSON. Outro exemplo é a API do Twitter, que nos permite recuperar os últimos Tweets de uma determinada conta nos formatos XML ou JSON, e ainda nos permite enviar um Tweet 'se autenticado', e assim por diante.

Para habilitar o uso de APIs em um aplicativo da web, os desenvolvedores precisam desenvolver essa funcionalidade no back-end do aplicativo da web usando os padrões de API como SOAP ou REST.

### SOAP

O padrão SOAP (Simple Objects Access) compartilha dados através de XML, onde a requisição é feita em XML através de uma requisição HTTP, e a resposta também é retornada em XML. Os componentes front-end são projetados para analisar essa saída XML corretamente. Veja a seguir um exemplo de mensagem SOAP:

```xml
<?xml version="1.0"?>

<soap:Envelope
xmlns:soap="http://www.example.com/soap/soap/"
soap:encodingStyle="http://www.w3.org/soap/soap-encoding">

<soap:Header>
</soap:Header>

<soap:Body>
  <soap:Fault>
  </soap:Fault>
</soap:Body>

</soap:Envelope>
```

SOAP é muito útil para transferir dados estruturados (ou seja, um objeto de classe inteiro), ou mesmo dados binários, e é frequentemente usado com objetos serializados, o que permite o compartilhamento de dados complexos entre componentes front-end e back-end e analisando-os corretamente. Também é muito útil para compartilhar objetos com estado -ou seja, compartilhar/alterar o estado atual de uma página da web-, que está se tornando mais comum com aplicativos da web modernos e aplicativos móveis.

No entanto, o SOAP pode ser difícil de usar para iniciantes ou exigir solicitações longas e complicadas, mesmo para consultas menores, como pesquisa básica ou consultas de filtro. É aqui que o padrão da API REST é mais útil.

### REST

O padrão REST (Representational State Transfer) compartilha dados por meio do caminho da URL 'i.e. search/users/1', e geralmente retorna a saída no formato JSON 'i.e. ID do usuário 1'.

Ao contrário dos parâmetros de consulta, as APIs REST geralmente se concentram em páginas que esperam um tipo de entrada transmitida diretamente pelo caminho da URL, sem especificar seu nome ou tipo. Isso geralmente é útil para consultas como pesquisa, classificação ou filtro. É por isso que as APIs REST geralmente dividem a funcionalidade do aplicativo da Web em APIs menores e utilizam essas solicitações de API menores para permitir que o aplicativo da web execute ações mais avançadas, tornando o aplicativo da web mais modular e escalável.

As respostas às solicitações da API REST geralmente são feitas no formato JSON e os componentes front-end são desenvolvidos para lidar com essa resposta e renderizá-la adequadamente. Outros formatos de saída para REST incluem XML, x-www-form-urlencoded ou até mesmo dados brutos. Conforme visto anteriormente na seção do banco de dados, o seguinte é um exemplo de uma resposta JSON para a solicitação GET /category/posts/ API:

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

