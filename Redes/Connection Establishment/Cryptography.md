
A criptografia é usada na Internet para transmitir dados, como informações de pagamento, e-mails ou dados pessoais, de forma confidencial e protegida contra manipulação. Os dados são criptografados usando vários algoritmos criptográficos baseados em operações matemáticas. Com a ajuda da criptografia, os dados podem ser transformados em um formato que pessoas não autorizadas não possam mais ler. Chaves digitais em processos de criptografia simétrica ou assimétrica são usadas para criptografia. É mais fácil decifrar textos cifrados ou chaves, dependendo dos métodos de criptografia usados. Se forem usados ​​métodos criptográficos de última geração com comprimentos de chave extensos, eles funcionam com muita segurança e são quase impossíveis de comprometer por enquanto. Em princípio, podemos distinguir entre técnicas de criptografia simétricas e assimétricas. Os métodos assimétricos são conhecidos há apenas algumas décadas. No entanto, são os métodos mais utilizados na comunicação digital.

# Symmetric Encryption

A criptografia simétrica, também conhecida como criptografia de chave secreta, é um método que usa a mesma chave para criptografar e descriptografar os dados. Isso significa que o remetente e o destinatário devem ter a mesma chave para descriptografar os dados corretamente.

Se a chave secreta for compartilhada ou perdida, a segurança dos dados não é mais garantida. Ações críticas para métodos de criptografia simétrica representam a distribuição, armazenamento e troca das chaves. O padrão de criptografia avançada (AES) e o padrão de criptografia de dados (DES) são exemplos de algoritmos de criptografia simétrica. Esse tipo de criptografia geralmente é usado para criptografar grandes quantidades de dados, como arquivos em um disco rígido ou dados enviados por uma rede. AES é considerado o algoritmo de criptografia mais seguro atualmente.

# Asymmetric Encryption

A criptografia assimétrica, também conhecida como criptografia de chave pública, é um método de criptografia que usa duas chaves diferentes:
- uma chave pública
- uma chave privada

A chave pública é usada para criptografar os dados, enquanto a chave privada é usada para descriptografar os dados. Isso significa que qualquer pessoa pode usar uma chave pública para criptografar dados para alguém, mas apenas o destinatário com a chave privada associada pode descriptografar os dados.

Exemplos de métodos de criptografia assimétrica incluem Rivest–Shamir–Adleman (RSA), Pretty Good Privacy (PGP) e Elliptic Curve Cryptography (ECC). A criptografia assimétrica é usada em uma variedade de aplicações, algumas das quais incluem:

- E-Signatures
- SSL/TLS
- SSH
- PKI
- VPNs
- Cloud

# Public-Key Encription

Uma vantagem da criptografia assimétrica é sua segurança. Como a segurança é baseada em problemas matemáticos muito difíceis de resolver, ataques simples não podem quebrá-la. Além disso, a questão da troca de chaves é contornada. Este é um problema significativo com métodos de criptografia simétrica. No entanto, como a chave pública pode ser acessível a todos, não há necessidade de trocar chaves secretamente. Além disso, os métodos assimétricos abrem a possibilidade de autenticação com assinaturas digitais.

# Data Encryption Standard

O DES é uma cifra de bloco de chave simétrica e sua criptografia funciona como uma combinação das cifras de chave única, permutação e substituição aplicadas a sequências de bits. Ele usa a mesma chave para criptografar e descriptografar dados.

A chave consiste em 64 bits, com 8 bits usados ​​como soma de verificação. Portanto, o tamanho real da chave do DES é de apenas 56 bits. E é por isso que sempre se fala de um comprimento de chave de 56 bits quando se refere a DES. Para evitar o perigo da análise de frequência, não letras únicas, mas cada bloco de 64 bits de texto simples é criptografado em um bloco de 64 bits de texto cifrado.

Uma extensão do DES é o chamado Triple DES / 3DES, que criptografa os dados com mais segurança. O procedimento para isso geralmente consiste em três chaves, sendo a primeira chave usada para criptografar os dados, a segunda para descriptografar os dados e a terceira para criptografá-los novamente.

O 3DES foi considerado mais seguro que o DES original porque oferece maior segurança usando três rodadas de criptografia, embora o uso de uma chave de 56 bits ainda o limite. O AES, o sucessor do DES, oferece maior segurança usando comprimentos de chave mais longos e agora é a tecnologia de criptografia simétrica mais amplamente usada.

# Advanced Encryption Standard

Comparado ao DES, o AES usa chaves de 128 bits (AES-128), 192 bits (AES-192) ou 256 bits (AES-256) para criptografar e descriptografar dados. Além disso, O AES é mais rápido que o DES porque possui uma estrutura de algoritmo mais eficiente. Isso ocorre porque ele pode ser aplicado a vários blocos de dados de uma só vez, tornando-o mais rápido. Isso significa que a criptografia e descriptografia AES podem ser executadas mais rapidamente do que DES, o que é especialmente importante quando grandes quantidades de dados precisam ser criptografadas.

Por exemplo, podemos encontrar AES em muitas aplicações e protocolos diferentes, mas não se limitam a:
- WLAN IEEE 802.11i
- VoIP
- IPsec
- PGP
- SSH
- OpenSSL

# Cipher Modes

Um modo de cifra refere-se a como um algoritmo de cifra de bloco criptografa uma mensagem de texto simples. Um algoritmo de cifra de bloco criptografa dados, cada um usando blocos de dados de tamanho fixo (geralmente 64 ou 128 bits). Um modo de cifra define como esses blocos são processados ​​e combinados para criptografar uma mensagem de qualquer comprimento. Existem vários modos de cifra comuns, incluindo:

|**Cipher Mode**|**Description**|
|---|---|
|[Electronic Code Book](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation) (`ECB`) mode|ECB mode is generally not recommended for use due to its susceptibility to certain types of attacks. Furthermore, it does not hide data patterns efficiently. As a result, statistical analysis can reveal elements of clear-text messages, for example, in web applications.|
|[Cipher Block Chaining](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CBC) (`CBC`) mode|CBC mode is generally used to encrypt messages like disk encryption and e-mail communication. This is the default mode for AES and is also used in software like TrueCrypt, VeraCrypt, TLS, and SSL.|
|[Cipher Feedback](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher_feedback_(CFB)) (`CFB`) mode|CFB mode is well suited for real-time encryption of a data stream, e.g., network communication encryption or encryption/decryption of files in transit like Public-Key Cryptography Standards (PKCS) and Microsoft's BitLocker.|
|[Output Feedback](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#OFB) (`OFB`) mode|OFB mode is also used to encrypt a data stream, e.g., to encrypt real-time communication. However, this mode is considered better for the data stream because of how the key stream is generated. We can find this mode in PKCS but also in the SSH protocol.|
|[Counter](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (`CTR`) mode|CTR mode encrypts real-time data streams AES uses, e.g., network communication, disk encryption, and other real-time scenarios where data is processed. An example of this would be IPsec or Microsoft's BitLocker.|
|[Galois/Counter](https://en.wikipedia.org/wiki/Galois/Counter_Mode) (`GCM`) mode|GCM is used in cases where confidentiality and integrity need to be protected together, such as wireless communications, VPNs, and other secure communication protocols.|

Cada modo tem suas características e é mais adequado para determinados casos de uso. A escolha do modo de criptografia depende dos requisitos do aplicativo e dos objetivos de segurança a serem alcançados. 