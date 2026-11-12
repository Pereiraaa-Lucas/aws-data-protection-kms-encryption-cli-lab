# 🔐 AWS Data Protection: Cloud Encryption with AWS KMS & Encryption CLI

Este repositório documenta a implementação prática de uma estratégia de **Proteção de Dados e Criptografia em Nuvem** utilizando o **AWS Key Management Service (AWS KMS)** e a **AWS Encryption CLI** em uma instância do **Amazon EC2** (File Server).

O projeto demonstra a capacidade de criar chaves simétricas seguras, configurar credenciais e comandos de criptografia via linha de comando, proteger arquivos de texto contendo dados sensíveis (*plaintext*) e realizar o ciclo completo de criptografia (`--encrypt`) e descriptografia (`--decrypt`).

---

## 🏗️ Arquitetura e Cenário do Laboratório

O ambiente simula um servidor de arquivos em nuvem que necessita proteger dados confidenciais contra acessos não autorizados por meio de criptografia forte gerenciada por hardware validado.

- **Servidor de Arquivos:** Instância do **Amazon EC2** gerenciada via **AWS Systems Manager (Session Manager)**.
- **Camada de Criptografia:** **AWS KMS** (**Key Management Service**) com chaves simétricas protegidas por **HSMs validados FIPS 140-2**.
- **Ferramental de Execução:** **AWS Encryption CLI** instalada via `pip3` na instância Linux.

---

## ⚙️ Principais Componentes Implementados

### 1. Criação e Gerenciamento de Chaves no AWS KMS
- **Tipo de Chave:** Chave **Simétrica** (utilizada para criptografar e descriptografar dados de forma rápida e eficiente).
- **Alias e Descrição:** `MyKMSKey` — *Key used to encrypt and decrypt data files*.
- **Controle de Acesso:** Atribuição de permissões administrativas ao perfil de IAM do laboratório (`voclabs`).
- **Identificação:** Obtenção e uso do **ARN da Chave** (*Amazon Resource Name*) para referência em variáveis de ambiente.

### 2. Configuração do Servidor de Arquivos e AWS Encryption CLI
- Configuração do arquivo de credenciais do AWS CLI (`~/.aws/credentials`) utilizando as credenciais temporárias do ambiente.
- Instalação da ferramenta **AWS Encryption CLI** (`aws-encryption-sdk-cli`) no ambiente Linux.
- Exportação correta do caminho executável no **PATH** do sistema (`~/.local/bin`).

### 3. Criptografia de Dados Simétricos (`--encrypt`)
- Criação de arquivos de texto de teste (`secret1.txt`, `secret2.txt`, `secret3.txt`) contendo dados confidenciais simulados.
- Execução do comando de criptografia utilizando o SDK CLI:
  - Parâmetro `--encrypt` para especificação da operação.
  - Parâmetro `--wrapping-keys` associado à chave do KMS (`$keyArn`).
  - Uso de **Contexto de Criptografia** (`purpose=test`) e política de compromisso (`require-encrypt-require-decrypt`).
- Validação do sucesso da operação através do código de saída do sistema (`echo $? == 0`) e visualização do arquivo resultante (`secret1.txt.encrypted`).

### 4. Descriptografia e Recuperação de Dados (`--decrypt`)
- Execução do comando reverso utilizando a mesma chave do KMS para reverter o texto cifrado (*Ciphertext*) de volta ao formato legível (*Plaintext*).
- Validação e inspeção do arquivo descriptografado (`secret1.txt.encrypted.decrypted`).

---

## 🚀 Passo a Passo da Implementação

### Passo 1: Criar a Chave no AWS KMS
1. No Console da AWS, acessar o **Key Management Service (KMS)**.
2. Clicar em **Create a key** (*Criar uma chave*).
3. Selecionar o tipo **Symmetric** (*Simétrica*).
4. Preencher o **Alias** como `MyKMSKey` e a descrição correspondente.
5. Definir as permissões administrativas para o usuário/perfil `voclabs` e concluir.
6. Copiar e salvar o **ARN da chave** gerado para utilização posterior.

### Passo 2: Configurar o Servidor de Arquivos (EC2 & CLI)
1. Conectar-se à instância **File Server** via **AWS Systems Manager (Session Manager)**.
2. Executar os comandos de configuração inicial das credenciais:
   ```bash
   cd ~
   aws configure

1. Atualizar o arquivo ~/.aws/credentials com as credenciais fornecidas pelo painel de detalhes do ambiente (incluindo o token de sessão).

2. Instalar a AWS Encryption CLI e atualizar o PATH:
      ```bash
      pip3 install aws-encryption-sdk-cli
      export PATH=$PATH:/home/ssm-user/.local/bin

Passo 3: Criar, Criptografar e Descriptografar Arquivos
1. Criar os arquivos de texto e definir o conteúdo sensível:
   ```bash
         touch secret1.txt secret2.txt secret3.txt
         echo 'TOP SECRET 1!!!' > secret1.txt
         mkdir output

 2. Salvar o ARN da chave em uma variável de ambiente e executar a criptografia:
     ```bash
     keyArn="<COLE_SEU_KMS_ARN_AQUI>"
     aws-encryption-cli --encrypt \
                   --input secret1.txt \
                   --wrapping-keys key=$keyArn \
                   --metadata-output ~/metadata \
                   --encryption-context purpose=test \
                   --commitment-policy require-encrypt-require-decrypt \
                   --output ~/output/.

 3. Descriptografar o arquivo gerado:
     ```bash
     cd output
     aws-encryption-cli --decrypt \
                   --input secret1.txt.encrypted \
                   --wrapping-keys key=$keyArn \
                   --commitment-policy require-encrypt-require-decrypt \
                   --encryption-context purpose=test \
                   --metadata-output ~/metadata \
                   --max-encrypted-data-keys 1 \
                   --buffer \
                   --output .

  🛠️ Tecnologias e Ferramentas Utilizadas
AWS KMS (Key Management Service): Gerenciamento seguro de chaves criptográficas em módulos HSM validados.

Amazon EC2 & Systems Manager: Computação em nuvem segura e acesso via Session Manager sem portas abertas.

AWS Encryption CLI: Ferramenta de linha de comando para criptografia e integridade de dados baseada no AWS Encryption SDK.

Data Security & Cryptography: Princípios de confidencialidade, chaves simétricas e criptografia em repouso.

👨‍💻 Autor
Lucas dos Santos Pereira

Estudante de Análise e Desenvolvimento de Sistemas | Focado em Engenharia de Nuvem, Infraestrutura e Segurança da Informação.
