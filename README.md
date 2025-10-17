# nested-reusable-java-maven-eks

Este repositório contém workflows reutilizáveis para projetos Java com Spring Boot, Maven e integração com EKS (Elastic Kubernetes Service).
Cada workflow pode ser usado de forma independente, dependendo das necessidades do repositório que o reutilizará.
Este repositório foi criado para atender aos micro serviços criados na FIAP. 

## Workflows Disponíveis

### 1. Build Workflow
Responsável por validação, lint, build e testes unitários.

**Arquivo**: `.github/workflows/build.yml`

**Inputs**:
- `app_name` (opcional): Nome do aplicativo.
- `dockerhub_username` (opcional): Usuário do Docker Hub.
- `cluster_name` (opcional): Nome do cluster EKS.
- `region_aws_deploy` (opcional): Região AWS.
- `domain_name` (opcional): Nome do domínio.
- `run_validation_and_lint` (obrigatório): Executar validação e lint.
- `run_build` (obrigatório): Executar build Maven.
- `run_unit_test` (obrigatório): Executar testes unitários.

### 2. Gates Workflow
Executa o SonarQube Scan para análise de qualidade do código.

**Arquivo**: `.github/workflows/gates.yml`

**Inputs**:
- `run_sonar_scan` (obrigatório): Executar SonarQube Scan.

### 3. Deploy Workflow
Realiza o build e push da imagem Docker e o deploy no cluster EKS.

**Arquivo**: `.github/workflows/deploy.yml`

**Inputs**:
- `app_name` (opcional): Nome do aplicativo.
- `dockerhub_username` (opcional): Usuário do Docker Hub.
- `cluster_name` (opcional): Nome do cluster EKS.
- `region_aws_deploy` (opcional): Região AWS.
- `domain_name` (opcional): Nome do domínio.
- `run_docker_build_and_push` (obrigatório): Executar build e push Docker.
- `run_deploy` (obrigatório): Executar deploy no cluster.

## Como Reutilizar os Workflows

1. **Referencie o workflow desejado**:
   No repositório que utilizará o workflow, adicione um arquivo no diretório `.github/workflows` com o seguinte conteúdo:

Exemplo para o `build.yml`:
```yaml
name: Build Workflow

on:
  push:
    branches:
      - main
  workflow_dispatch:
    inputs:
      run_validation_and_lint:
        description: 'Executar Validation & Code Quality'
        required: true
        default: true
        type: boolean
      run_build:
        description: 'Executar Build Maven'
        required: true
        default: true
        type: boolean
      run_unit_test:
        description: 'Executar Testes Unitários'
        required: true
        default: true
        type: boolean

jobs:
  build-workflow:
    uses: rachelkozlowsky/nested-reusable-java-maven-eks/.github/workflows/build.yml
    with:
      run_validation_and_lint: ${{ github.event.inputs.run_validation_and_lint }}
      run_build: ${{ github.event.inputs.run_build }}
      run_unit_test: ${{ github.event.inputs.run_unit_test }}
```
## Configurações:

- Certifique-se de que os valores necessários estão configurados:

Use o arquivo pipe.yml ou configure variáveis e secrets no GitHub.

- Certifique-se de que possui um arquivo sonar-project.properties

Use o arquivo sonar-project.properties para configurar o SonarQube.

- Execute o workflow:

O workflow pode ser acionado manualmente via workflow_dispatch.

## Configuração de Variáveis e Secrets

Adicione as variáveis e secrets necessários em Settings > Secrets and variables > Actions no repositório que reutilizará os workflows.


**Variáveis**

- APP_NAME
- DOCKERHUB_USERNAME
- CLUSTER_NAME
- REGION_AWS_DEPLOY
- DOMAIN_NAME

**Secrets**

- DOCKERHUB_TOKEN
- AWS_ACCESS_KEY_ID
- AWS_SECRET_ACCESS_KEY
- SONAR_TOKEN
- SONAR_HOST_URL
- Outros secrets específicos do projeto.

Com essas informações, você pode reutilizar os workflows de forma modular e eficiente.