# azure-devops-pipeline

## Objetivo
    Padrão de pipelines para ser implantado visando a criação de templates de varias tecnologias
## Tecnologias
- Azure DevOps
- SonarQube
- Helm Charts
  

## Estrutura e Arquivos
```
├── repo
│   ├── azure-pipeline.yaml
│   └── sonar-project.properties
├── repo-multiaplicacao
│   ├── app01
│   │   └── azure-pipeline.yaml
│   └── app02
│       └── azure-pipeline.yaml
└── repo-pipeline-templates
    ├── helm_template
    │   ├── Chart.yaml
    │   ├── templates
    │   │   ├── _helpers.tpl
    │   │   ├── configmap.yaml
    │   │   ├── deployment.yaml
    │   │   ├── hpa.yaml
    │   │   ├── ingress.yaml
    │   │   ├── namespace.yaml
    │   │   └── service.yaml
    │   ├── values-example.yaml
    │   └── values.yaml
    ├── release.yaml
    ├── template-dotnet.yaml
    ├── template-nodejs.yaml
    └── vars.yaml
```
### Legenda
| Path                                              | Descrição                                                                                                                                                                                                                                                                                       |
|---------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **repo**                                          | **Repositório** com uma unica aplicação, no exemplo uma aplicação **Node.js**                                                                                                                                                                                                                   |
| repo>**azure-pipeline.yaml**                      | Configurações de parametros usados pelo template-nodejs.yaml                                                                                                                                                                                                                                    |
| repo>**sonar-project.properties**                 | Configuração do SonarQube para aplicações **Node.js**                                                                                                                                                                                                                                           |
| **repo-multiaplicacao**                           | **Repositório** com varias aplicações segregadas por diretório, no exemplo aplicações DotNet                                                                                                                                                                                                    |
| repo-multiaplicacao>**app01**                     | **Diretório** onde se encontra todo o código de uma aplicação DotNet Core e o arquivo de pipeline.                                                                                                                                                                                              |
| repo-multiaplicacao>app01>**azure-pipeline.yaml** | Configurações de parametros para a aplicação app01 que são passados para o template-dotnet.yaml                                                                                                                                                                                                 |
| repo-multiaplicacao>**app02**                     | **Diretório** onde se encontra todo o código de uma segunda aplicação DotNet Core e o arquivo de pipeline                                                                                                                                                                                       |
| repo-multiaplicacao>app02>**azure-pipeline.yaml** | Configurações de parametros app02 que são passados para o template-dotnet.yaml                                                                                                                                                                                                                  |
| **repo-pipeline-templates**                       | **Repositório** apartado com todos os templates necessários para build e deploy                                                                                                                                                                                                                 |
| repo-pipeline-templates>**helm_template**         | **Diretório** onde se encontra toda estrtura de arquivos de Helm Chart utilizados para deploy no k8s                                                                                                                                                                                            |
| repo-pipeline-templates>**release.yaml**          | Arquivo que contem a etapa de deploy que recebe os parametros vindo do stage de deploy que se encontra em cada um dos templates (template-dotnet.yaml e template-nodejs.yaml)                                                                                                                   |
| repo-pipeline-templates>**template-dotnet.yaml**  | Esse template contem todos os stages necessários para uma aplicação DotNet Core, sendo esses stages resumidos em: Analise estatica SonarQube, Build e Push da imagem para container regitry AWS, stage de deploy com parametros enviados para a etapa de deploy contida no arquivo release.yaml |
| repo-pipeline-templates>**template-nodejs.yaml**  | Esse template contem todos os stages necessários para uma aplicação Node.js, sendo esses stages resumidos em: Analise estatica SonarQube, Build e Push da imagem para container regitry AWS, stage de deploy com parametros enviados para a etapa de deploy contida no arquivo release.yaml     |
| repo-pipeline-templates>**vars.yaml**             | Variaveis Globais utilizadas pelos templates(template-dotnet.yaml e template-nodejs.yaml)                                                                                                                                                                                                       |

## Pré requisitos

### SonarQube

No azure DevOps verificar se tem o plugin instalado em <organization>/Settings/Extensions/Installed

Caso não esteja instalado, instalar ou solicitar para o administrador da organização instala-lo

Link para instalação: https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarqube

 #### SonarQube Sevice connection

1. No Azure DevOps, vá para  Project Settings > Service connections.
2. Selecione New service connection e selecione SonarQube na lista de service connection.
3. Insira o URL do SonarQube server, um token de autenticação e um nome de conexão de serviço memorável. Em seguida, selecione Salvar para salvar sua conexão.

⚠️É importante lembrar que o nome dessa service connection deve ser o mesmo nos arquivos de templates das pipelines (template-dotnet.yaml e template-nodejs.yaml )

### Configurar service connections dos demais serviços
 
 #### Kubernetes (usado para fazer deploy das aplicações)
  1. No Azure DevOps, vá para  Project Settings > Service connections.
  2. Selecione New service connection e selecione Kubernetes na lista de service connection.
  3. Selecione o Authentication method e faça as devidas configurações.
  
  ⚠️É importante lembrar que o nome dessa service connection deve ser o mesmo nos arquivos de templates das pipelines (template-dotnet.yaml e template-nodejs.yaml )

 #### AWS (usado no container registry)
  1. No Azure DevOps, vá para  Project Settings > Service connections.
  2. Selecione New service connection e selecione AWS na lista de service connection.
  3. Preencha os campos com sua Access Key ID e Secret Access Key.

  ⚠️É importante lembrar que o nome dessa service connection deve ser o mesmo nos arquivos de templates das pipelines (template-dotnet.yaml e template-nodejs.yaml )

### Criar a pipeline no Azure DevOps
  1. No Azure DevOps, acesse o projeto desejado.
  2. Na barra de menu esquerdo acesse pipelines.
  3. Clique no botão New pipeline.
  4. Selecione a opção Azure Repos Git.
  5. Selecione o repositório onde se encontra o arquivo azure-pipeline.yaml.
  6. Selecione a opção Existing Azure Pipelines YAML file.
  7. Selecione a Branch e o Path que o arquivo azure-pipeline.yaml se encontra, e clique em continue. 
