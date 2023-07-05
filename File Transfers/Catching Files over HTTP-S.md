
## HTTP/S

A transferência pela Web é a maneira mais comum pela qual a maioria das pessoas transfere arquivos porque `HTTP`/`HTTPS` são os protocolos mais comuns permitidos através de firewalls. Outro imenso benefício é que, em muitos casos, o arquivo será criptografado em trânsito. Não há nada pior do que estar em um teste de penetração, e o IDS de rede de um cliente capta um arquivo sensível sendo transferido por texto sem formatação e faz com que ele pergunte por que enviamos uma senha para o nosso servidor em nuvem sem usar criptografia.

Já discutimos o uso do módulo Python3 [uploadserver](https://github.com/Densaugeo/uploadserver) para configurar um servidor da Web com recursos de upload, mas também podemos usar o Apache ou o Nginx. Esta seção abordará a criação de um servidor da Web seguro para operações de upload de arquivos.

## Nginx - Habilitando o PUT

Uma boa alternativa para transferir arquivos para `Apache` é [Nginx](https://www.nginx.com/resources/wiki/) porque a configuração é menos complicada e o sistema do módulo não leva a problemas de segurança como `Apache` pode.

Ao permitir uploads `HTTP`, é fundamental ter 100% de certeza de que os usuários não podem fazer upload de shells da web e executá-los. `Apache` facilita atirar no pé com isso, como o `PHP` módulo adora executar qualquer coisa que termine em `PHP`. Configurando `Nginx` usar o PHP não é nem de longe tão simples.

#### Crie um diretório para manipular arquivos enviados

```shell-session
casluxd@htb[/htb]$ sudo mkdir -p /var/www/uploads/SecretUploadDirectory
```

#### Altere o proprietário para www-data

```shell-session
casluxd@htb[/htb]$ sudo chown -R www-data:www-data /var/www/uploads/SecretUploadDirectory
```

#### Criar arquivo de configuração do Nginx

Crie o arquivo de configuração Nginx criando o arquivo `/etc/nginx/sites-available/upload.conf` com o conteúdo:

```shell-session
server {
    listen 9001;
    
    location /SecretUploadDirectory/ {
        root    /var/www/uploads;
        dav_methods PUT;
    }
}
```

#### Simlink nosso site para o diretório habilitado para sites

```shell-session
casluxd@htb[/htb]$ sudo ln -s /etc/nginx/sites-available/upload.conf /etc/nginx/sites-enabled/
```

#### Iniciar Nginx

```shell-session
casluxd@htb[/htb]$ sudo systemctl restart nginx.service
```

Se recebermos alguma mensagem de erro, verifique `/var/log/nginx/error.log`. Se estiver usando o Pwnbox, veremos que a porta 80 já está em uso.

#### Verificando erros

```shell-session
casluxd@htb[/htb]$ tail -2 `/var/log/nginx/error.log`

2020/11/17 16:11:56 [emerg] 5679#5679: bind() to 0.0.0.0:`80` failed (98: A`ddress already in use`)
2020/11/17 16:11:56 [emerg] 5679#5679: still could not bind()
```

```shell-session
casluxd@htb[/htb]$ ss -lnpt | grep `80`

LISTEN 0      100          0.0.0.0:80        0.0.0.0:*    users:(("python",pid=`2811`,fd=3),("python",pid=2070,fd=3),("python",pid=1968,fd=3),("python",pid=1856,fd=3))
```

```shell-session
casluxd@htb[/htb]$ ps -ef | grep `2811`

user65      2811    1856  0 16:05 ?        00:00:04 `python -m websockify 80 localhost:5901 -D`
root        6720    2226  0 16:14 pts/0    00:00:00 grep --color=auto 2811
```

Vemos que já existe um módulo ouvindo na porta 80. Para contornar isso, podemos remover a configuração padrão do Nginx, que se liga à porta 80.

#### Remover configuração do NginxDefault

```shell-session
casluxd@htb[/htb]$ sudo rm /etc/nginx/sites-enabled/default
```

Agora podemos testar o upload usando `cURL` para enviar uma solicitação `PUT`. No exemplo abaixo, carregaremos o arquivo `/etc/passwd` para o servidor e chame-o de users.txt

#### Carregar arquivo usando o cURL

```shell-session
casluxd@htb[/htb]$ curl -T /etc/passwd 
http://localhost:9001/SecretUploadDirectory/users.txt
```

```shell-session
casluxd@htb[/htb]$ tail -1 /var/www/uploads/SecretUploadDirectory/users.txt 

user65:x:1000:1000:,,,:/home/user65:/bin/bash
```

Depois que isso funcionar, um bom teste é garantir que a listagem de diretórios não seja ativada navegando para `http://localhost/SecretUploadDirectory`. Por padrão, com `Apache`, se clicarmos em um diretório sem um arquivo de índice ( index.html ), ele listará todos os arquivos. Isso é ruim para o nosso caso de uso de arquivos exfiltrados, porque a maioria dos arquivos é sensível por natureza e queremos fazer o possível para ocultá-los. Graças a `Nginx` sendo mínimo, recursos como esse não são ativados por padrão.