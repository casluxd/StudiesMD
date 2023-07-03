Além do Kerberos e do LDAP, o Active Directory usa vários outros métodos de autenticação que podem ser usados ​​(e abusados) por aplicativos e serviços no AD. Isso inclui LM, NTLM, NTLMv1 e NTLMv2. LM e NTLM aqui são os nomes de hash, e NTLMv1 e NTLMv2 são protocolos de autenticação que utilizam o hash LM ou NT. Abaixo está uma rápida comparação entre esses hashes e protocolos, o que nos mostra que, embora não seja perfeito de forma alguma, o Kerberos costuma ser o protocolo de autenticação escolhido sempre que possível. É essencial entender a diferença entre os tipos de hash e os protocolos que os utilizam.

|**Hash/Protocol**|**Cryptographic technique**|**Mutual Authentication**|**Message Type**|**Trusted Third Party**|
|---|---|---|---|---|
|`NTLM`|Symmetric key cryptography|No|Random number|Domain Controller|
|`NTLMv1`|Symmetric key cryptography|No|MD4 hash, random number|Domain Controller|
|`NTLMv2`|Symmetric key cryptography|No|MD4 hash, random number|Domain Controller|
|`Kerberos`|Symmetric key cryptography & asymmetric cryptography|Yes|Encrypted ticket using DES, MD5|Domain Controller/Key Distribution Center (KDC)|

## LM

Os hashes do LAN Manager (LM ou LANMAN) são o mecanismo de armazenamento de senha mais antigo usado pelo sistema operacional Windows. LM estreou em 1987 no sistema operacional OS/2. Se estiverem em uso, eles são armazenados no banco de dados SAM em um host Windows e no banco de dados NTDS.DIT ​​em um controlador de domínio. Devido a falhas de segurança significativas no algoritmo de hash usado para hashes LM, ele foi desativado por padrão desde o Windows Vista/Server 2008. Porém, ainda é comum encontrar, principalmente em grandes ambientes onde ainda são utilizados sistemas mais antigos. As senhas que usam LM são limitadas a um máximo de 14 caracteres. As senhas não diferenciam maiúsculas de minúsculas e são convertidas em maiúsculas antes de gerar o valor de hash, limitando o keyspace a um total de 69 caracteres, tornando relativamente fácil quebrar esses hashes usando uma ferramenta como Hashcat.

Antes do hashing, uma senha de 14 caracteres é primeiro dividida em dois blocos de sete caracteres. Se a senha tiver menos de quatorze caracteres, ela será preenchida com caracteres NULL para atingir o valor correto. Esses pedaços são então criptografados usando a string KGS!@#$%, criando dois valores de texto cifrado de 8 bytes. Esses dois valores são então concatenados, resultando em um hash LM. Esse algoritmo de hash significa que um invasor só precisa usar força bruta sete caracteres duas vezes em vez de quatorze caracteres inteiros, tornando mais rápido quebrar hashes LM em um sistema com uma ou mais GPUs. Se uma senha tiver sete caracteres ou menos, a segunda metade do hash LM sempre terá o mesmo valor e pode até ser determinada visualmente sem ferramentas necessárias, como o Hashcat. O uso de hashes LM pode ser proibido usando a Diretiva de Grupo. Um hash LM assume a forma de 299bd128c1101fd6.

`Observação: os sistemas operacionais Windows anteriores ao Windows Vista e Windows Server 2008 (Windows NT4, Windows 2000, Windows 2003, Windows XP) armazenavam o hash LM e o hash NTLM da senha de um usuário por padrão.`

## NTHash (NTLM)

Os hashes NT LAN Manager (NTLM) são usados ​​em sistemas Windows modernos. É um protocolo de autenticação de desafio-resposta e usa três mensagens para autenticar: um cliente primeiro envia um NEGOTIATE_MESSAGE para o servidor, cuja resposta é um CHALLENGE_MESSAGE para verificar a identidade do cliente. Por fim, o cliente responde com um AUTHENTICATE_MESSAGE. Esses hashes são armazenados localmente no banco de dados SAM ou no arquivo de banco de dados NTDS.DIT ​​em um controlador de domínio. O protocolo tem dois valores de senha com hash para escolher para executar a autenticação: o hash LM (conforme discutido acima) e o hash NT, que é o hash MD4 do valor UTF-16 little-endian da senha. O algoritmo pode ser visualizado como: MD4(UTF-16-LE(senha)).

![[Pasted image 20230601223736.png]]

Embora sejam consideravelmente mais fortes do que os hashes LM (suportando todo o conjunto de caracteres Unicode de 65.536 caracteres), eles ainda podem ser forçados offline offline com relativa rapidez usando uma ferramenta como o Hashcat. Os ataques de GPU mostraram que todo o espaço-chave de caracteres NTLM 8 pode ser forçado em menos de 3 horas. Hashes NTLM mais longos podem ser mais difíceis de quebrar, dependendo da senha escolhida, e até mesmo senhas longas (mais de 15 caracteres) podem ser quebradas usando um ataque de dicionário offline combinado com regras. O NTLM também é vulnerável ao ataque pass-the-hash, o que significa que um invasor pode usar apenas o hash NTLM (após obter por meio de outro ataque bem-sucedido) para autenticar os sistemas de destino nos quais o usuário é um administrador local sem precisar saber o valor de texto não criptografado da senha.

Um hash NT assume a forma de b4b9b02e6f09a9bd760f388b67351e2b, que é a segunda metade do hash NTLM completo. Um hash NTLM se parece com isto:

````shell-session
Rachel:500:aad3c435b514a4eeaad3b935b51304fe:e46b9e548fa0d122de7f59fb6d48eaa2:::
````

Olhando para o hash acima, podemos dividir o hash NTLM em suas partes individuais:

- Rachel é o nome de usuário
- 500 é o identificador relativo (RID). 500 é o RID conhecido para a conta de administrador
- aad3c435b514a4eeaad3b935b51304fe é o hash LM e, se os hashes LM estiverem desabilitados no sistema, não pode ser usado para nada
- e46b9e548fa0d122de7f59fb6d48eaa2 é o hash do NT. Esse hash pode ser quebrado offline para revelar o valor de texto não criptografado (dependendo do comprimento/força da senha) ou usado para um ataque pass-the-hash. Abaixo está um exemplo de um ataque pass-the-hash bem-sucedido usando a ferramenta CrackMapExec:

```shell-session
casluxd@htb[/htb]$ crackmapexec smb 10.129.41.19 -u rachel -H e46b9e548fa0d122de7f59fb6d48eaa2

SMB         10.129.43.9     445    DC01      [*] Windows 10.0 Build 17763 (name:DC01) (domain:INLANEFREIGHT.LOCAL) (signing:True) (SMBv1:False)
SMB         10.129.43.9     445    DC01      [+] INLANEFREIGHT.LOCAL\rachel:e46b9e548fa0d122de7f59fb6d48eaa2 (Pwn3d!)
```

Agora que entendemos os recursos e a estrutura do NTLM, vamos examinar a progressão do protocolo por meio do NTLMv1 e do NTLMv2.

`Nota: Nem LANMAN nem NTLM usam sal.`

## NTLMv1 (Net-NTLMv1)

O protocolo NTLM executa um desafio/resposta entre um servidor e um cliente usando o hash NT. O NTLMv1 usa o hash NT e o LM, o que pode facilitar a "quebra" offline após a captura de um hash usando uma ferramenta como Responder ou por meio de um ataque de retransmissão NTLM(ambos estão fora do escopo deste módulo e serão abordados em módulos posteriores sobre Movimento Lateral). O protocolo é usado para autenticação de rede e o próprio hash Net-NTLMv1 é criado a partir de um algoritmo de desafio/resposta. O servidor envia ao cliente um número aleatório de 8 bytes (desafio) e o cliente retorna uma resposta de 24 bytes. Esses hashes NÃO podem ser usados ​​para ataques pass-the-hash. O algoritmo se parece com o seguinte:

#### V1 Challenge & Response Algorithm

```shell-session
C = 8-byte server challenge, random
K1 | K2 | K3 = LM/NT-hash | 5-bytes-0
response = DES(K1,C) | DES(K2,C) | DES(K3,C)
```

Um exemplo de um hash NTLMv1 completo se parece com:

```shell-session
u4-netntlm::kNS:338d08f8e26de93300000000000000000000000000000000:9526fb8c23a90751cdd619b6cea564742e1e4bf33006ba41:cb8086049ec4736c
```

O NTLMv1 foi o bloco de construção da autenticação NTLM moderna. Como qualquer protocolo, ele possui falhas e é suscetível a cracking e outros ataques. Agora, vamos seguir em frente e dar uma olhada no NTLMv2 e ver como ele melhora a base dessa versão.

#### V2 Challenge & Response Algorithm

```shell-session
SC = 8-byte server challenge, random
CC = 8-byte client challenge, random
CC* = (X, time, CC2, domain name)
v2-Hash = HMAC-MD5(NT-Hash, user name, domain name)
LMv2 = HMAC-MD5(v2-Hash, SC, CC)
NTv2 = HMAC-MD5(v2-Hash, SC, CC*)
response = LMv2 | CC | NTv2 | CC*
```

Um exemplo de um hash NTLMv2 é:

```shell-session
admin::N46iSNekpT:08ca45b7d7ea58ee:88dcbe4446168966a153a0064958dac6:5c7830315c7830310000000000000b45c67103d07d7b95acd12ffa11230e0000000052920b85f78d013c31cdb3b92f5d765c783030
```

Podemos ver que os desenvolvedores melhoraram a v1 tornando o NTLMv2 mais difícil de quebrar e dando a ele um algoritmo mais robusto composto de vários estágios. Temos mais um mecanismo de autenticação para discutir antes de prosseguir. Este método é importante para nós porque não requer uma conexão de rede persistente para funcionar.

## Domain Cached Credentials (MSCache2

Em um ambiente AD, os métodos de autenticação mencionados nesta seção e na anterior requerem que o host que estamos tentando acessar se comunique com o "cérebro" da rede, o controlador de domínio. A Microsoft desenvolveu o algoritmo MS Cache v1 e v2 (também conhecido como Domain Cached Credentials (DCC) para resolver o possível problema de um host ingressado no domínio ser incapaz de se comunicar com um controlador de domínio (ou seja, devido a uma interrupção de rede ou outro problema técnico) e, portanto, a autenticação NTLM/Kerberos não está funcionando para acessar o host em questão. Os hosts salvam os últimos dez hashes para todos os usuários de domínio que fizerem login com êxito na máquina na chave de registro `HKEY_LOCAL_MACHINE\SECURITY\Cache`. Esses hashes não podem ser usados ​​em ataques pass-the-hash. Além disso, o hash é muito lento para quebrar com uma ferramenta como o Hashcat, mesmo ao usar um equipamento de quebra de GPU extremamente poderoso, portanto, as tentativas de quebrar esses hashes geralmente precisam ser extremamente direcionadas ou dependem de uma senha muito fraca em uso. Esses hashes podem ser obtidos por um invasor ou pentester após obter acesso de administrador local a um host e têm o seguinte formato: `$DCC2$10240#bjones#e4e938d12fe5974dc42a90120bd9c90f`. É vital como testadores de penetração entendermos os vários tipos de hashes que podemos encontrar ao avaliar um ambiente de AD, seus pontos fortes, fracos, como eles podem ser abusados (cracking to cleartext, pass-the-hash ou relayed) e quando um ataque pode ser inútil (ou seja, passar dias tentando quebrar um conjunto de credenciais em cache de domínio).