O uso de nuvem, como [AWS](https://aws.amazon.com/), [GCP](https://cloud.google.com/), [Azure](https://azure.microsoft.com/en-us/), e outros, agora é um dos componentes essenciais para muitas empresas atualmente. Afinal, todas as empresas querem poder fazer seu trabalho de qualquer lugar, por isso precisam de um ponto central para toda a administração. É por isso que serviços de `Amazon` (`AWS`), `Google` (`GCP`) e `Microsoft` (`Azure`) são ideais para esse fim.

Embora os provedores de nuvem protejam sua infraestrutura centralmente, isso não significa que as empresas estejam livres de vulnerabilidades. As configurações feitas pelos administradores podem, no entanto, tornar os recursos em nuvem da empresa vulneráveis. Isso geralmente começa com o `S3 buckets` ( AWS ), `blobs` ( Azure ), `cloud storage` ( GCP ), que pode ser acessado sem autenticação se configurado incorretamente.

#### Servidores hospedados na empresa

```shell-session
casluxd@htb[/htb]$ for i in $(cat subdomainlist);do host $i | grep "has address" | grep inlanefreight.com | cut -d" " -f1,4;done

blog.inlanefreight.com 10.129.24.93
inlanefreight.com 10.129.27.33
matomo.inlanefreight.com 10.129.127.22
www.inlanefreight.com 10.129.127.33
s3-website-us-west-2.amazonaws.com 10.129.95.250
```

Muitas vezes, o armazenamento em nuvem é adicionado à lista de DNS quando usado para fins administrativos por outros funcionários. Essa etapa facilita muito a obtenção e o gerenciamento dos funcionários. Vamos ficar com o caso de uma empresa nos contratar e, durante a pesquisa de IP, já vimos que um endereço IP pertence ao servidor `s3-website-us-west-2.amazonaws.com`.

No entanto, existem muitas maneiras diferentes de encontrar esse armazenamento em nuvem. Uma das mais fáceis e usadas é a pesquisa do Google combinada com o Google Dorks. Por exemplo, podemos usar as dorks do Google `inurl:` e `intext:` restringir nossa pesquisa a termos específicos. No exemplo a seguir, vemos áreas censuradas em vermelho contendo o nome da empresa.

![[Pasted image 20230618170859.png]]

![[Pasted image 20230618170902.png]]

Aqui já podemos ver que os links apresentados pelo Google contêm PDFs. Quando procuramos por uma empresa que já possamos conhecer ou querer conhecer, também encontraremos outros arquivos, como documentos de texto, apresentações, códigos e muitos outros.

Esse conteúdo também é frequentemente incluído no código fonte das páginas da web, de onde as imagens, códigos JavaScript ou CSS são carregados. Esse procedimento geralmente alivia o servidor da web e não armazena conteúdo desnecessário.

![[Pasted image 20230618171013.png]]

Provedores terceirizados, como [domain.glass](https://domain.glass/) também pode nos contar muito sobre a infraestrutura da empresa. Como efeito colateral positivo, também podemos ver que o status de avaliação de segurança do Cloudflare foi classificado como "seguro". Isso significa que já encontramos uma medida de segurança que pode ser observada para a segunda camada ( gateway ).

![[Pasted image 20230618171029.png]]

Outro provedor muito útil é [GrayHatWarfare](https://buckets.grayhatwarfare.com/). Podemos fazer muitas pesquisas diferentes, descobrir o armazenamento em nuvem AWS, Azure e GCP e até classificar e filtrar por formato de arquivo. Portanto, depois de encontrá-los no Google, também podemos procurá-los no GrayHatWarefare e descobrir passivamente quais arquivos são armazenados no armazenamento em nuvem fornecido.

![[Pasted image 20230618171205.png]]

Muitas empresas também usam abreviações do nome da empresa, que são usadas de acordo com a infraestrutura de TI. Esses termos também fazem parte de uma excelente abordagem para descobrir novos armazenamentos em nuvem da empresa. Também podemos procurar arquivos simultaneamente para ver os arquivos que podem ser acessados ao mesmo tempo.

#### Chaves SSH privadas e públicas vazadas

![[Pasted image 20230618171228.png]]

Às vezes, quando os funcionários estão sobrecarregados ou sob alta pressão, os erros podem ser fatais para toda a empresa. Esses erros podem até levar ao vazamento de chaves privadas SSH, que qualquer pessoa pode baixar e fazer login em uma ou mais máquinas da empresa sem usar uma senha.

![[Pasted image 20230618171239.png]]

