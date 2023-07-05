Como testadores de penetração, geralmente obtemos acesso a dados altamente sensíveis, como listas de usuários, credenciais ( ou seja, baixando o arquivo NTDS.dit para cracking de senha offline ), e dados de enumeração que podem conter informações críticas sobre a infraestrutura de rede da organização e o ambiente do Active Directory ( AD ), etc. Portanto, é essencial criptografar esses dados ou usar conexões de dados criptografadas, como SSH, SFTP e HTTPS. No entanto, às vezes essas opções não estão disponíveis para nós e é necessária uma abordagem diferente.

`Nota: A menos que solicitado especificamente por um cliente, não recomendamos a exfiltração de dados como Informações de identificação pessoal ( PII ), dados financeiros ( ou seja, números de cartão de crédito ), segredos comerciais etc., de um ambiente cliente. Em vez disso, se tentar testar as proteções de filtragem de controles / saída de prevenção de perda de dados (crie um arquivo com dados fictícios que imitem os dados que o cliente está tentando proteger.)`

Portanto, criptografar os dados ou arquivos antes de uma transferência geralmente é necessário para impedir que os dados sejam lidos se interceptados em trânsito.

O vazamento de dados durante um teste de penetração pode ter graves consequências para o testador de penetração, sua empresa e o cliente. Como profissionais de segurança da informação, devemos agir de maneira profissional e responsável e tomar todas as medidas para proteger todos os dados que encontrarmos durante uma avaliação.

## Criptografia de arquivo no Windows

Muitos métodos diferentes podem ser usados para criptografar arquivos e informações em sistemas Windows. Um dos métodos mais simples é o [Invoke-AESEncryption.ps1](https://www.powershellgallery.com/packages/DRTools/4.0.2.3/Content/Functions%5CInvoke-AESEncryption.ps1) Script PowerShell. Esse script é pequeno e fornece criptografia de arquivos e strings.

#### Invoke-AESEncryption.ps1

```powershell-session
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Text "Secret Text" 

Description
-----------
Encrypts the string "Secret Test" and outputs a Base64 encoded ciphertext.
 
.EXAMPLE
Invoke-AESEncryption -Mode Decrypt -Key "p@ssw0rd" -Text "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs="
 
Description
-----------
Decrypts the Base64 encoded string "LtxcRelxrDLrDB9rBD6JrfX/czKjZ2CUJkrg++kAMfs=" and outputs plain text.
 
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin
 
Description
-----------
Encrypts the file "file.bin" and outputs an encrypted file "file.bin.aes"
 
.EXAMPLE
Invoke-AESEncryption -Mode Encrypt -Key "p@ssw0rd" -Path file.bin.aes
 
Description
-----------
Decrypts the file "file.bin.aes" and outputs an encrypted file "file.bin"
#>
function Invoke-AESEncryption {
    [CmdletBinding()]
    [OutputType([string])]
    Param
    (
        [Parameter(Mandatory = $true)]
        [ValidateSet('Encrypt', 'Decrypt')]
        [String]$Mode,

        [Parameter(Mandatory = $true)]
        [String]$Key,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptText")]
        [String]$Text,

        [Parameter(Mandatory = $true, ParameterSetName = "CryptFile")]
        [String]$Path
    )

    Begin {
        $shaManaged = New-Object System.Security.Cryptography.SHA256Managed
        $aesManaged = New-Object System.Security.Cryptography.AesManaged
        $aesManaged.Mode = [System.Security.Cryptography.CipherMode]::CBC
        $aesManaged.Padding = [System.Security.Cryptography.PaddingMode]::Zeros
        $aesManaged.BlockSize = 128
        $aesManaged.KeySize = 256
    }

    Process {
        $aesManaged.Key = $shaManaged.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($Key))

        switch ($Mode) {
            'Encrypt' {
                if ($Text) {$plainBytes = [System.Text.Encoding]::UTF8.GetBytes($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "File not found!"
                        break
                    }
                    $plainBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName + ".aes"
                }

                $encryptor = $aesManaged.CreateEncryptor()
                $encryptedBytes = $encryptor.TransformFinalBlock($plainBytes, 0, $plainBytes.Length)
                $encryptedBytes = $aesManaged.IV + $encryptedBytes
                $aesManaged.Dispose()

                if ($Text) {return [System.Convert]::ToBase64String($encryptedBytes)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $encryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "File encrypted to $outPath"
                }
            }

            'Decrypt' {
                if ($Text) {$cipherBytes = [System.Convert]::FromBase64String($Text)}
                
                if ($Path) {
                    $File = Get-Item -Path $Path -ErrorAction SilentlyContinue
                    if (!$File.FullName) {
                        Write-Error -Message "File not found!"
                        break
                    }
                    $cipherBytes = [System.IO.File]::ReadAllBytes($File.FullName)
                    $outPath = $File.FullName -replace ".aes"
                }

                $aesManaged.IV = $cipherBytes[0..15]
                $decryptor = $aesManaged.CreateDecryptor()
                $decryptedBytes = $decryptor.TransformFinalBlock($cipherBytes, 16, $cipherBytes.Length - 16)
                $aesManaged.Dispose()

                if ($Text) {return [System.Text.Encoding]::UTF8.GetString($decryptedBytes).Trim([char]0)}
                
                if ($Path) {
                    [System.IO.File]::WriteAllBytes($outPath, $decryptedBytes)
                    (Get-Item $outPath).LastWriteTime = $File.LastWriteTime
                    return "File decrypted to $outPath"
                }
            }
        }
    }

    End {
        $shaManaged.Dispose()
        $aesManaged.Dispose()
    }
}
```

Podemos usar qualquer método de transferência de arquivo mostrado anteriormente para colocar esse arquivo em um host de destino. Após a transferência do script, ele só precisa ser importado como um módulo, conforme mostrado abaixo.

#### Módulo de importação Invoke-AESEncryption.ps1

```powershell-session
PS C:\htb> Import-Module .\Invoke-AESEncryption.ps1
```

Após a importação do script, ele pode criptografar cadeias ou arquivos, conforme mostrado nos exemplos a seguir. Este comando cria um arquivo criptografado com o mesmo nome que o arquivo criptografado, mas com a extensão "`.aes`."

#### Exemplo de criptografia de arquivo

```powershell-session
PS C:\htb> Invoke-AESEncryption.ps1 -Mode Encrypt -Key "p4ssw0rd" -Path .\scan-results.txt

File encrypted to C:\htb\scan-results.txt.aes
PS C:\htb> ls

    Directory: C:\htb

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a----        11/18/2020  12:17 AM           9734 Invoke-AESEncryption.ps1
-a----        11/18/2020  12:19 PM           1724 scan-results.txt
-a----        11/18/2020  12:20 PM           3448 scan-results.txt.aes
```

Usando senhas muito `strong` e `unique` para criptografia para todas as empresas em que um teste de penetração é realizado são essenciais. Isso evita que arquivos e informações confidenciais sejam descriptografados usando uma única senha que pode ter sido vazada e quebrada por terceiros.

## Criptografia de arquivo no Linux

[OpenSSL](https://www.openssl.org/) é frequentemente incluído nas distribuições Linux, com os administradores de sistemas usando-o para gerar certificados de segurança, entre outras tarefas. O OpenSSL pode ser usado para enviar arquivos "estilo nc" para criptografar arquivos.

Para criptografar um arquivo usando `openssl` podemos selecionar cifras diferentes, veja [Página de manual do OpenSSL](https://www.openssl.org/docs/man1.1.1/man1/openssl-enc.html). Vamos usar `-aes256` como um exemplo. Também podemos substituir as contagens de iterações padrão com a opção `-iter 100000` e adicione a opção `-pbkdf2` para usar o algoritmo 2 da função de derivação de chave baseada em senha. Quando clicarmos em Enter, precisaremos fornecer uma senha.

#### Criptografando / etc / passwd com openssl

```shell-session
casluxd@htb[/htb]$ openssl enc -aes256 -iter 100000 -pbkdf2 -in /etc/passwd -out passwd.enc

enter aes-256-cbc encryption password:                                                         
Verifying - enter aes-256-cbc encryption password:                              
```

Lembre-se de usar uma senha forte e exclusiva para evitar ataques de rachaduras por força bruta, caso uma parte não autorizada obtenha o arquivo. Para descriptografar o arquivo, podemos usar o seguinte comando:

#### Decrypt passwd.enc com openssl

```shell-session
casluxd@htb[/htb]$ openssl enc -d -aes256 -iter 100000 -pbkdf2 -in passwd.enc -out passwd                    

enter aes-256-cbc decryption password:
```

Podemos usar qualquer um dos métodos anteriores para transferir esse arquivo, mas é recomendável usar um método de transporte seguro, como HTTPS, SFTP ou SSH. Como sempre, pratique os exemplos nesta seção em relação aos hosts de destino neste ou em outros módulos e reproduza o que você pode ( como o `openssl` exemplos usando o Pwnbox. A seção a seguir cobrirá diferentes maneiras de transferir arquivos por HTTP e HTTPS.