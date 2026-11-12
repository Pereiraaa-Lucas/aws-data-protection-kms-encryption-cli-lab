# 🔐 AWS Data Protection: KMS Key Management & Encryption CLI Lab

Este repositório documenta a implementação prática de estratégias avançadas de **Proteção de Dados e Criptografia em Nuvem** utilizando o **AWS Key Management Service (AWS KMS)** e a **AWS Encryption Command Line Interface (CLI)** em um ambiente corporativo simulado no **Amazon EC2**.

O projeto demonstra a capacidade de criar, governar e utilizar chaves criptográficas simétricas gerenciadas por hardware (**HSMs FIPS 140-2**), executando operações locais de cifra e decifra para garantir a confidencialidade e a integridade de dados sensíveis.

---

## 🏗️ Arquitetura e Cenário do Laboratório

O ambiente simula um servidor de arquivos corporativo onde chaves criptográficas controlam estritamente o acesso a dados confidenciais.

- **Servidor Gerenciado:** Instância **Amazon EC2** (`File Server`) acessada de forma segura via **AWS Systems Manager Session Manager**.
- **Serviço de Chaves:** **AWS KMS** (**Key Management Service**) para gerenciamento centralizado de chaves simétricas.
- **Ferramenta de Criptografia:** **AWS Encryption CLI** (`aws-encryption-sdk-cli`) instalada no ambiente Linux para operações de conversão de dados.

---

## ⚙️ Principais Componentes Implementados

### 1. Criação de Chave Simétrica no AWS KMS
- **Tipo de Chave:** **Simétrica** (utiliza a mesma chave para criptografar e descriptografar, garantindo máxima performance e eficiência).
- **Alias e Identificação:** `MyKMSKey` associada ao seu respectivo **ARN** (*Amazon Resource Name*).
- **Governança de Permissões:** Concessão de controle administrativo e de uso ao perfil de **IAM** do laboratório (`voclabs`).

### 2. Configuração do Servidor de Arquivos e Credenciais
- Configuração segura das credenciais temporárias da AWS (`~/.aws/credentials`) na instância do EC2.
- Instalação do utilitário **AWS Encryption CLI** via `pip3` e exportação do executável para o caminho do sistema (`PATH`).

### 3. Criptografia de Dados (Plaintext para Ciphertext)
- Criação de arquivos de texto simulando dados confidenciais (`secret1.txt`, `secret2.txt`, `secret3.txt`).
- Execução do comando de criptografia utilizando o ARN da chave do KMS, contexto de criptografia e política de confirmação de chave:
  ```bash
  aws-encryption-cli --encrypt \
                      --input secret1.txt \
                      --wrapping-keys key=$keyArn \
                      --metadata-output ~/metadata \
                      --encryption-context purpose=test \
                      --commitment-policy require-encrypt-require-decrypt \
                      --output ~/output/.
  ```
- Validação do sucesso da operação (`echo $?` igual a `0`) e inspeção do arquivo cifrado gerado (`secret1.txt.encrypted`).

### 4. Descriptografia e Recuperação de Dados
- Execução do comando de decifra para reverter o texto cifrado de volta ao formato original legível:
  ```bash
  aws-encryption-cli --decrypt \
                      --input secret1.txt.encrypted \
                      --wrapping-keys key=$keyArn \
                      --commitment-policy require-encrypt-require-decrypt \
                      --encryption-context purpose=test \
                      --metadata-output ~/metadata \
                      --max-encrypted-data-keys 1 \
                      --buffer \
                      --output .
  ```
- Validação do conteúdo original recuperado com sucesso em `secret1.txt.encrypted.decrypted`.

---

## 🚀 Passo a Passo da Implementação

### Passo 1: Criação da Chave no AWS KMS
1. No console da AWS, acessar o **Key Management Service (KMS)**.
2. Criar uma nova chave do tipo **Symmetric**.
3. Atribuir o alias `MyKMSKey` e a descrição `Key used to encrypt and decrypt data files`.
4. Definir as permissões administrativas e de uso para o perfil `voclabs`.
5. Copiar o **ARN da chave** gerado para utilização nas etapas seguintes.

### Passo 2: Configuração da Instância File Server
1. Acessar o console do **Amazon EC2**, selecionar a instância **File Server** e conectar via **AWS Systems Manager Session Manager**.
2. Configurar as credenciais temporárias da AWS em `~/.aws/credentials` utilizando as chaves de acesso e token fornecidos pelo ambiente Vocareum.
3. Instalar a CLI de criptografia:
   ```bash
   pip3 install aws-encryption-sdk-cli
   export PATH=$PATH:/home/ssm-user/.local/bin
   ```

### Passo 3: Operações de Criptografia e Descriptografia
1. Criar os arquivos de teste e definir o conteúdo sigiloso em `secret1.txt`:
   ```bash
   touch secret1.txt secret2.txt secret3.txt
   echo 'TOP SECRET 1!!!' > secret1.txt
   ```
2. Salvar o ARN da chave KMS em uma variável de ambiente: `keyArn=<SEU_ARN_KMS>`.
3. Executar o comando `aws-encryption-cli --encrypt` para gerar o arquivo cifrado na pasta `output/`.
4. Executar o comando `aws-encryption-cli --decrypt` para restaurar o documento original e validar o conteúdo com `cat`.

---

## 🛠️ Tecnologias e Ferramentas Utilizadas

- **AWS EC2 (File Server):** Infraestrutura computacional para processamento local.
- **AWS KMS:** Serviço seguro de gestão de chaves criptográficas validadas por padrões **FIPS 140-2**.
- **AWS Encryption CLI:** Utilitário de linha de comando para segurança baseada em criptografia.
- **AWS Systems Manager (Session Manager):** Acesso remoto seguro via shell sem necessidade de portas SSH abertas.

---

## 👨‍💻 Autor

**Lucas dos Santos Pereira**  
*Estudante de Análise e Desenvolvimento de Sistemas | Focado em Engenharia de Nuvem, Infraestrutura e Automação.*
