# 🔒 AWS Data Protection & Encryption Lab: AWS KMS & Encryption CLI

Este repositório documenta a implementação prática de uma estratégia de **Proteção de Dados e Criptografia em Repouso**, utilizando o **AWS Key Management Service (AWS KMS)** em conjunto com a **AWS Encryption CLI** em uma instância do **Amazon EC2 (File Server)**.

O projeto demonstra competência em governança de chaves criptográficas simétricas, configuração de credenciais via **AWS Systems Manager (Session Manager)**, e execução de operações seguras de cifragem (`--encrypt`) e decifragem (`--decrypt`) de arquivos sensíveis.

---

## 🏗️ Arquitetura e Cenário do Laboratório

O ambiente simula um servidor de arquivos corporativo onde dados confidenciais em texto simples (*Plaintext*) são transformados em texto cifrado (*Ciphertext*) utilizando chaves gerenciadas com segurança de hardware (**FIPS 140-2**).

- **Infraestrutura:** Instância **Amazon EC2** configurada como *File Server*.
- **Serviço de Chaves:** **AWS KMS** (criação de chave simétrica personalizada `MyKMSKey`).
- **Ferramenta de Criptografia:** **AWS Encryption CLI** instalada no ambiente Linux via `pip3`.
- **Acesso Operacional:** **AWS Systems Manager - Session Manager** integrado com perfis de **IAM**.

---

## ⚙️ Principais Componentes Implementados

### 1. Criação e Gestão de Chaves no AWS KMS
- Criação de uma chave **Simétrica** personalizada (`MyKMSKey`) com descrição e escopo de segurança definidos.
- Concessão de privilégios administrativos e de uso para o perfil do IAM associado ao laboratório (`voclabs`).
- Extração e armazenamento seguro do **ARN da Chave** (`Key ARN`) para uso nas variáveis de ambiente da CLI.

### 2. Configuração de Credenciais e Ambiente na Instância
- Configuração do arquivo de credenciais da AWS (`~/.aws/credentials`) na instância do EC2.
- Instalação do pacote `aws-encryption-sdk-cli` utilizando o gerenciador de pacotes Python (`pip3`).
- Exportação correta do caminho binário (`PATH`) para execução global dos comandos de criptografia.

### 3. Criptografia de Dados Sensíveis (`--encrypt`)
- Criação de arquivos de simulação de dados sigilosos (`secret1.txt`, `secret2.txt`, `secret3.txt`).
- Execução do comando de cifragem utilizando o ARN da chave do KMS e o contexto de criptografia (`purpose=test`):
  - Parâmetro `--commitment-policy require-encrypt-require-decrypt` para reforçar a segurança criptográfica.
  - Salvamento automatizado do arquivo cifrado no diretório de saída (`output/`).
- Validação de sucesso por meio do código de retorno do sistema (`echo $?` igual a `0`).

### 4. Descriptografia e Recuperação de Dados (`--decrypt`)
- Reversão do texto cifrado (`secret1.txt.encrypted`) para texto simples legível utilizando a mesma chave simétrica KMS.
- Auditoria de integridade do conteúdo descriptografado (`secret1.txt.encrypted.decrypted`).

---

## 🚀 Passo a Passo da Implementação

### Passo 1: Criação da Chave Simétrica no AWS KMS
1. No Console da AWS, pesquisar e acessar o **Key Management Service (KMS)**.
2. Clicar em **Create a key** (*Criar uma chave*).
3. Selecionar o tipo de chave **Symmetric** (*Simétrica*) e clicar em **Next**.
4. Definir o **Alias**: `MyKMSKey` e a **Description**: `Key used to encrypt and decrypt data files`.
5. Selecionar as permissões administrativas e de uso para o usuário/perfil `voclabs`.
6. Finalizar a criação, localizar a chave criada (`MyKMSKey`) e copiar o seu **ARN** para um editor de texto.

### Passo 2: Configuração da Instância do Servidor de Arquivos (EC2 & CLI)
1. Acessar o console do **Amazon EC2**, selecionar a instância **File Server** e clicar em **Connect** > **Session Manager** > **Connect**.
2. No terminal, configurar as credenciais temporárias da AWS:
   ```bash
   cd ~
   aws configure

   (Inserir 1 para Access Key ID e Secret Access Key, e colar a região padrão obtida no painel de detalhes do laboratório).
3. Atualizar o arquivo ~/.aws/credentials com as credenciais definitivas fornecidas pelo ambiente.
4. Instalar a AWS Encryption CLI e ajustar o PATH:
   
pip3 install aws-encryption-sdk-cli
export PATH=$PATH:/home/ssm-user/.local/bin

Passo 3: Criptografia de Arquivos de Texto
1. Criar os arquivos de teste e o diretório de saída:

touch secret1.txt secret2.txt secret3.txt
echo 'TOP SECRET 1!!!' > secret1.txt
mkdir output

2. Armazenar o ARN da chave em uma variável de ambiente:
   keyArn="<COLE_O_ARN_DO_KMS_AQUI>"

3.  Executar o comando de criptografia:
   aws-encryption-cli --encrypt \
                   --input secret1.txt \
                   --wrapping-keys key=$keyArn \
                   --metadata-output ~/metadata \
                   --encryption-context purpose=test \
                   --commitment-policy require-encrypt-require-decrypt \
                   --output ~/output/.

4. Validar o sucesso da operação (echo $? deve retornar 0) e verificar o arquivo gerado (secret1.txt.encrypted).

   Passo 4: Descriptografia do Arquivo Cifrado

1. Executar o comando de descriptografia na pasta de saída:
   aws-encryption-cli --decrypt \
                   --input secret1.txt.encrypted \
                   --wrapping-keys key=$keyArn \
                   --commitment-policy require-encrypt-require-decrypt \
                   --encryption-context purpose=test \
                   --metadata-output ~/metadata \
                   --max-encrypted-data-keys 1 \
                   --buffer \
                   --output .

2. Visualizar o conteúdo recuperado:
   cat secret1.txt.encrypted.decrypted

   🛠️ Tecnologias e Ferramentas Utilizadas
AWS KMS (Key Management Service): Gestão segura de chaves de criptografia validadas por FIPS 140-2.

AWS Encryption CLI: Ferramenta de linha de comando para criptografia do lado do cliente (Client-Side Encryption).

Amazon EC2 & Systems Manager: Computação em nuvem e acesso seguro via Session Manager.

Data Security & Cryptography: Práticas de confidencialidade, integridade e proteção de dados em repouso.

👨‍💻 Autor
Lucas dos Santos Pereira

Estudante de Análise e Desenvolvimento de Sistemas | Focado em Engenharia de Nuvem, Infraestrutura e Segurança da Informação.

---

### 💡 Nome sugerido para o Repositório no GitHub:
`aws-data-protection-kms-encryption-cli-lab`
