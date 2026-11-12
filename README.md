🛡️ AWS Key Management Service & Encryption CLI: Laboratório Prático de Proteção de Dados
Este repositório documenta a implementação prática de estratégias de Proteção de Dados Usando Criptografia na nuvem AWS. O laboratório aborda a criação de chaves criptográficas gerenciadas por hardware, configuração de credenciais em ambiente de servidor de arquivos via Amazon EC2 e AWS Systems Manager (Session Manager), além da utilização da AWS Encryption CLI para cifrar e decifrar arquivos de texto confidencial de maneira segura.

🏗️ Arquitetura e Cenário do Laboratório
O ambiente simula um servidor de arquivos em nuvem que exige forte proteção de dados em repouso por meio de criptografia simétrica validada por padrões de segurança.

Infraestrutura Gerenciada: Instância do Amazon EC2 configurada como File Server.

Camada de Criptografia: AWS Key Management Service (AWS KMS) para gerenciamento seguro de chaves simétricas protegidas por módulos de segurança de hardware (HSMs) validados pelo padrão FIPS 140-2.

Ferramenta de Linha de Comando: AWS Encryption CLI (aws-encryption-sdk-cli) para automação de processos de criptografia e descriptografia diretamente no terminal.

⚙️ Principais Componentes Implementados
1. Criação e Gestão de Chaves no AWS KMS (MyKMSKey)
Configuração de uma chave de criptografia Simétrica, otimizada para eficiência e desempenho em operações de cifra e decifra.

Atribuição de permissões administrativas e de uso para o perfil do IAM (voclabs) associado ao laboratório.

Geração e salvamento do ARN (Amazon Resource Name) da chave para referência em variáveis de ambiente.

2. Configuração do Ambiente no Amazon EC2
Acesso seguro ao terminal da instância do File Server utilizando o AWS Systems Manager (Session Manager).

Configuração inicial do arquivo de credenciais da AWS (~/.aws/credentials) injetando as credenciais temporárias do ambiente do laboratório (Access Key ID, Secret Access Key e Session Token).

Instalação do pacote Python via pip3 para a AWS Encryption CLI (aws-encryption-sdk-cli) e exportação do caminho binário para o PATH do sistema.

3. Criptografia de Dados em Texto Simples (Plaintext ➔ CipherText)
Criação de arquivos de simulação de dados confidenciais (secret1.txt, secret2.txt, secret3.txt).

Execução do comando aws-encryption-cli --encrypt utilizando parâmetros avançados de segurança:

Contexto de Criptografia: Adição de metadados de integridade associados (purpose=test).

Política de Compromisso (Commitment Policy): Exigência de políticas robustas de confirmação de chave (require-encrypt-require-decrypt).

Isolamento de Saída: Gravação do arquivo criptografado (secret1.txt.encrypted) no diretório dedicado output/.

4. Descriptografia e Recuperação de Dados (CipherText ➔ Plaintext)
Execução do comando inverso aws-encryption-cli --decrypt utilizando a mesma chave KMS e o contexto de criptografia correspondente.

Validação do arquivo resultante (secret1.txt.encrypted.decrypted) e leitura do conteúdo original em texto simples via comandos nativos de terminal (cat).

🚀 Passo a Passo da Implementação
Passo 1: Criação da Chave Simétrica no AWS KMS
No console da AWS, pesquise por KMS e acesse o Key Management Service.

Clique em Create a key (Criar uma chave).

Selecione o tipo Symmetric (Simétrica) e clique em Next.

Configure os rótulos da chave:

Alias: MyKMSKey

Description: Key used to encrypt and decrypt data files.

Selecione as permissões administrativas e de uso para o perfil voclabs.

Revise as configurações, clique em Finish e copie o ARN da chave criada para um editor de texto.

Passo 2: Configuração da Instância do Servidor de Arquivos
Acesse o console do Amazon EC2, selecione a instância File Server, clique em Connect e utilize a guia Session Manager.

No terminal, configure o arquivo de credenciais com as chaves fornecidas no ambiente Vocareum:

```Bash
cd ~
vi ~/.aws/credentials

Substitua o conteúdo pelo bloco de credenciais padrão ([default] contendo aws_access_key_id, aws_secret_access_key e aws_session_token) e salve o arquivo (:wq).

Instale a AWS Encryption CLI e atualize o caminho do sistema:

```Bash
pip3 install aws-encryption-sdk-cli
export PATH=$PATH:/home/ssm-user/.local/bin

Passo 3: Criptografia de Dados Sensíveis
Crie os arquivos de texto e insira dados confidenciais:

```Bash
touch secret1.txt secret2.txt secret3.txt
echo 'TOP SECRET 1!!!' > secret1.txt
mkdir output
Defina a variável de ambiente com o ARN da chave KMS e execute a criptografia:

```Bash
keyArn="<COLE_O_ARN_DA_SUA_CHAVE_AQUI>"

aws-encryption-cli --encrypt \
                   --input secret1.txt \
                   --wrapping-keys key=$keyArn \
                   --metadata-output ~/metadata \
                   --encryption-context purpose=test \
                   --commitment-policy require-encrypt-require-decrypt \
                   --output ~/output/.
Validere o sucesso da operação (echo $? retornando 0) e verifique o arquivo gerado em output/secret1.txt.encrypted.

Passo 4: Descriptografia e Validação do Conteúdo
Execute o comando de descriptografia apontando para o arquivo cifrado:

````Bash
aws-encryption-cli --decrypt \
                   --input secret1.txt.encrypted \
                   --wrapping-keys key=$keyArn \
                   --commitment-policy require-encrypt-require-decrypt \
                   --encryption-context purpose=test \
                   --metadata-output ~/metadata \
                   --max-encrypted-data-keys 1 \
                   --buffer \
                   --output .
Visualize o conteúdo recuperado para garantir a integridade dos dados:

```Bash
cat secret1.txt.encrypted.decrypted

🛠️ Tecnologias e Ferramentas Utilizadas
Amazon EC2: Servidor de arquivos virtualizado em ambiente Linux.

AWS Key Management Service (AWS KMS): Gestão segura de chaves criptográficas com suporte a HSM.

AWS Systems Manager (Session Manager): Conexão segura e interativa via terminal sem necessidade de portas SSH abertas.

AWS Encryption CLI: Ferramenta de linha de comando para operações de criptografia e proteção de dados baseadas no AWS Encryption SDK.

👨‍💻 Autor
Lucas dos Santos Pereira

Estudante de Análise e Desenvolvimento de Sistemas | Focado em Engenharia de Nuvem, Infraestrutura e Segurança da Informação.
