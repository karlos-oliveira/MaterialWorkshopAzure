--------------------------------------------------------------------------------------
Preparando o ambiente
--------------------------------------------------------------------------------------
- Git instalado -> https://git-scm.com/downloads/
- Docker instalado -> https://www.docker.com/products/docker-desktop
- Criar uma conta no Visual Studio Dev Essentials -> https://visualstudio.microsoft.com/pt-br/dev-essentials/

--------------------------------------------------------------------------------------
Habilitando seus creditos Gratis
--------------------------------------------------------------------------------------
- Acessar https://portal.azure.com/
- Na pagina inicial, procure o card referente a avaliação gratuita do Azure
- Clique em Iniciar e siga as etapas descritas na tela, pode ser necessário colocar o seu cartão de crédito no processo porém a microsoft garante que nenhuma cobrança será feita sem seu conscentimento
- Feito o cadastro e a liberação dos 30 dias gratuitos, siga para os proximos passos

--------------------------------------------------------------------------------------
Configurando o Docker
--------------------------------------------------------------------------------------
- Após instalar o docker em sua máquina é importante (para esse tutorial) que seus containers sejam baseados em Linux
- Se estiver no ambiente windows, procure o docker em sua barra de sistema, clique com o botão direito sobre ele e clique em "Switch to Linux Containers"
- Isso é possivel graças ao WSL do windows 10
- Caso encontre algum erro, é possivel que seja necessário instalar uma atualização que se encontra em -> https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
- Depois de instalado, basta ir novamente em "Switch to Linux Containers"

--------------------------------------------------------------------------------------
Criando seu Agent Pool Self Hosted
--------------------------------------------------------------------------------------
- Acesse https://dev.azure.com/
- No Canto inferior esquerdo, clique em "Organization Settings"
- Clique em "Agent Pools" e depois em "Add pool"
- Em "Pool type" selecione "Self Hosted" e de um nome de sua preferencia (no meu caso darei o nome de "Carlos Pipeline")
- Certifique-se que os 2 campos abaixo estarão selecionados
	- Grant access permission to all pipelines
	- Auto-provision this agent pool in all projects
- Clique em "Create"

Criando seu Agent

- Clique no Agent Pool que acabou de criar (no meu caso "Carlos Pipeline")
- Navegue até a aba Agents e clique em "New agent"
- No PopUp que aparecerá, selecione o seu sistema operacional e clique no botão de "Download" e pode fechar o popup
- Crie uma pasta com o nome que preferir, aqui chamarei ela de "agent"
- Extraia o conteudo do zip que baixamos nas etapas anteriores para essa pasta

Configurando seu Agent

- Abra o powershell, navegue até a pasta onde você extraiu o zip baixado e execute o arquivo "config.cmd"
	- PS C:\Pasta\Onde\Extraiu> .\config.cmd
- Agora a ferramenta vai pedir algumas informações sobre seu server no AzureDevOps
	- Server Url -> Acesse https://dev.azure.com/ e copie a url que estiver no seu navegador, deve ser algo como "https://dev.azure.com/SuaOrganização"
	- Authentication type -> só tem uma opção disponivel (PAT), então basta apertar "Enter"	
	- Personal access token (PAT) -> no canto superior direito ao lado das suas iniciais, tem um botão que remete ao "User Settings", clique neste botão e depois clique em personal user tokens
		- Na proxima tela, clique em "New Token"
		- No campo Name, defina um nome a sua escolha, eu colocarei "Agent Access"
		- No final da tela, clique no link "Show all scopes"
		- suba o scroll da tela e encontre o contexto "Agent Pools" e selecione "Read & manage"
		- Clique no botão "Create"
		- Nesse momento será mostrado para você um campo de texto com um codigo, esse será seu PAT
		- Clique no botão que parecem duas folhas para copiar o código sem erros
	- Volte no seu Powershell e clique com o botão direito do mouse para colar seu codigo PAT e aperte "Enter"
	- agora seu Agent Pool self hosted já esta conectado, vamos terminar de configurá-lo
	- Agent Pool -> coloque o nome do agent pool que criou lá nos primeiros passos, no meu caso "Carlos Pipeline" e aperte "Enter"
	- Agent Name -> Esse é o nome da maquina em que aquele Agent está rodando, vou colocar como "NoteBookCarlos" e depois, aperte "Enter"
	- Work Folder - Vamos deixar padrão, basta apertar "Enter"
	- Run as Service -> Vamos deixar padrão, basta apertar "Enter"
	- Configure Autologin and run -> Vamos deixar padrão, basta apertar "Enter"
	- Prontinho, agora nosso agent está configurado, mas ainda precisamos rodá-lo, para isso execute o arquivo "run.cmd"
		- PS C:\Pasta\Onde\Extraiu> .\run.cmd

	- Agora se voltar em "Organization Settings" -> "Agent Pools" -> seu agent pool criado -> aba "Agents", verá seu agent self hosted online e esperando para executar os jobs

--------------------------------------------------------------------------------------
Criando um Registro de contêiner
--------------------------------------------------------------------------------------
  - Acessar https://portal.azure.com/
  - Pesquisar por "Registros de contêiner"
  - Clicar em Criar "registro de conteiner"
    - Assinatura: Escolher a assinatura
    - Grupo de Recursos: Selecionar ou criar um grupo de recursos, no meu caso vou criar o grupo "tutorial"
    - Nome do Registro: Criar o nome do registro, eu colocarei "carlosazregistry" (importante guardar esse nome)
    - Localização: Deixar como está
    - SKU: Selecionar o SKU Basico
    - Clicar em "Revisar + Criar" e depois clicar em "Criar"
    - Aguarde terminar a implantação e clique em "Ir para o recurso"
  - Nosso Registro de Container esta criado, mas ainda precisamos configurar uma conta de admnistrador, esse passo é importante mais para frente

Habilitar conta de administrador (https://docs.microsoft.com/en-us/azure/container-registry/container-registry-authentication#admin-account)
    - Na pagina do registro de conteiner que acabou de criar, procure  o item "Chaves de acesso"
    - Encontre o campo "Usuario administrador" e o habilite

--------------------------------------------------------------------------------------
Enviando seu código para o Azure
--------------------------------------------------------------------------------------
- Acesse https://dev.azure.com/
- Selecione seu projeto
- No canto esquerdo clique em "Repos"
- O seu repositório estará vazio, no bloco "Push an existing repository from command line", selecione o primeiro comando, algo como:
	- git remote add origin https://SuaOrganização@dev.azure.com/SuaOrganização/SeuProjeto/_git/SeuProjeto
- Agora vá na pasta do seu projeto clique com o botão direito e selecione "Git Bash Here"
- Quando o terminal abrir execute os seguintes comandos: 
	- git init (para iniciar o git na sua pasta local)
	- git remote add origin https://SuaOrganização@dev.azure.com/SuaOrganização/SeuProjeto/_git/SeuProjeto (para vincular seu repositorio local com seu repositório no DevOps) **Lembre-se de colocar as informações do seu projeto nesse comando
	- git add . (para adicionar todos os arquivos modificados para no commit)
	- git commit -m "Commit Inicial" (para versionar localmente suas modificações)
	- git push -u origin --all (para enviar seu código para o servidor)
- Acesse novamente o https://dev.azure.com/
- Selecione seu projeto e verá que agora seu código já está versionado

--------------------------------------------------------------------------------------
Configurando o Projeto
--------------------------------------------------------------------------------------
- Para que nosso projeto tenha as permissões necessárias para criar a esteira, fazer o build e o deploy, temos que adicionar algumas conexões primeiro para isso:

- Acessar https://dev.azure.com/
- Clicar no card correspondente ao seu projeto
- No Canto inferior esquerdo, clique em "Project Settings"
- Clicar em "Service Connections"
- Clicar em "Create Service Connection"
- O primeiro serviço que vamos conectar é o "Azure Resource Manager", portanto busque, selecione o mesmo e clique en "Next"
- Na proxima tela selecione "Service principal (automatic)" e clique em "Next"
- Aguarde ele carregar sua subscrição e tente selecionar o resource group
- Caso não carregue, desabilite seu anti popup e tente novamente, ele abrirá um popup para você se autenticar
- depois de autenticado e com o resource group selecionado, escolha um nome para sua conexão no campo "Service connection name", eu colocarei "Resource Manager"
- Garanta que o campo "Grant access permission to all pipelines" esteja selecionado
- Clique em "Save", você deve ver na lista a service conection que acabou de criar

- Agora vamos criar a service conection do conteiner registry
- Clicar em "Service Connections"
- Clicar em "New Service Connection"
- Clicar em "Docker Registry"
- Selecione "Azure Conteiner Registry"
- Aguarde ele carregar sua subscrição e selecione seu conteiner registry
- Escolha um nome para sua conexão no campo "Service connection name", eu colocarei "carlosazregistry"
- Garanta que o campo "Grant access permission to all pipelines" esteja selecionado
- Clique em "Save", você deve ver na lista a service conection que acabou de criar

--------------------------------------------------------------------------------------
Criando sua Esteira - Build
--------------------------------------------------------------------------------------
- Existem diversas formas de criar sua esteira no azure, para não me extender muito, vou focar em uma delas

Ajustando os arquivos para a esteira

- Nos arquivos disponibilizados, há dois arquivos de templates (Dockerfile e azure-pipelines.yml), vamos copiar os 2 para o diretório da nossa solution
- Com o visual studio code (ou qualquer outro editor de sua preferencia) vamos abrir o Dockerfile
- Este arquivo está praticamente pronto, tudo que temos que fazer é substituir a string "NAME-OF-YOUR-PROJECT" pelo nome da nossa solution, que aqui no meu caso é "TempoApi"
- Feito isso vamos salvar o arquivo Dockerfile e abrir o arquivo azure-pipelines.yml
- O arquivo azure-pipelines.yml também está praticamente rponto, sendo necessário apenas preencher algumas variáveis
	- substitua API-DISPLAY-NAME pelo nome amigavel da sua api, geralmente é o mesmo nome que ficará no endereço da mesma no meu caso vou colocar "azcarlosapitempo-microservice"
	- substitua CONTAINER-REGISTRY-NAME pelo nome do seu registro de conteiner que foi criado no começo desse tutorial, no meu caso é "carlosazregistry"
	- substitua AGENT-POOL-NAME pelo nome do seu agent pool, no meu caso é "Carlos Pipeline", lembrando que essa linha não é necessária em ambientes corporativos ou inscrições pagas
- Prontinho, os arquivos necessários para a nossa pipeline estão prontos, agora basta subirmos eles para o DevOps, e vamos aproveitar para criar a branch release também com os comandos abaixo:
	- git add .
	- git commit -m "Inclusão dos arquivos de esteira"
	- git checkout -b release
	- git push -u origin --all


Criando o pipeline

- Acessar https://dev.azure.com/
- Clicar no card correspondente ao seu projeto
- No menu esquerdo, clicar no botão "Pipelines" e depois em "Create Pipeline"
- Como vamos criar nosso pipeline a partir de um arquivo yaml que está no nosso projeto, selecione "Azure Repos Git"
- Selecione seu projeto
- Na próxima tela será aberto o arquivo yaml que está no nosso projeto, caso não selecione automativo, selecione manualmente o mesmo.
- Clique em "Run"
- Vai aparecer um Erro, basta clicar em "Authorize Resources"
- No menu esquerdo, clicar no botão "Pipelines"
- Selecione sua pipeline e clique em "Run Pipeline"
- Selecione a branch desejada e clique em "Run"
- Pronto, sua esteira já esta buildando seu código via esteira

--------------------------------------------------------------------------------------
Criando seu Web App
--------------------------------------------------------------------------------------
- Acessar https://portal.azure.com/
- Pesquisar por "Serviços de aplicativos"
- Clicar em "Adicionar" ou "Criar serviço de aplicativo"
- Nesta primeira tela, basta preencher as informações conforme abaixo:
	- Assinatura -> Selecione a sua subscrição do azure
	- Grupo de Recursos -> Selecione um existente ou crie um novo
	- Nome -> é a url pelo qual você vai acessar sua API, no meu caso vou colocar "azcarlosapitempo"
	- Publicar -> Selecione "Conteiner do Docker"
	- Sistema Operacional -> Selecione "Linux"
	- Região -> Pode deixar como está
	- Plano do Linux -> Caso não apareça nenhum, basta criar um novo no menor tamanho disponivel, que geralmente é o "Basico B1"
- Clique em "Avançar: Docker"
- Nesta tela vamos preencher conforme abaixo:
	- Opções -> Selecione "Conteiner Unico"
	- Origem da Imagem -> Selecione "Registro de conteiner do Azure"
	- Registro -> Selecione o registro de conteiner que criamos anteriormente
	- Imagem -> Selecione a imagem criada pelo build que fizemos via pipiline, esse nome é definido no arquivo azure-pipelines.yml
	- Marcação -> Selecione "latest"
- Tudo pronto, agora basta clicar em "Revisar + Criar"
- Verifique se está tudo ok e, caso positivo, clique em "Criar"
- Aguarde a implantação e assim que terminar, clique em "Ir para o recurso"
- Nesse passo você criou o recurso que receberá sua aplicação, mas ainda não tem nada lá, se acessarmos, vamos receber um erro
- Vamos para o próximo passo para entregar nossa API via esteira de Deploy.

--------------------------------------------------------------------------------------
Criando sua Esteira - Deploy
--------------------------------------------------------------------------------------
Após criar sua pipeline para buildar seu projeto, agora é hora de efetuar o deploy de sua aplicação

- Acessar https://dev.azure.com/
- Clicar no card correspondente ao seu projeto
- No menu esquerdo, clicar no botão "Pipelines" e depois no sub-item "Releases"
- Clique em "New Pipeline"
- Surgirá uma tela para você selecionar um template, existem inumeros templates para inumeras linguagens, mas para o nosso caso vamos usar o "Azure App Service Deployment"
- Com o "Azure App Service Deployment" selecionado, clique em "Apply"
- Na Tela a seguir, no campo "Stage name" vamos trocar para algo mais semantico, colocarei "Homologação"
- Feito isso vamos fechar esse popup e clicar na primeira caixa onde podemos ler "Add an Artifact"
	- Em "Source Type", selecione "Build", pois queremos que o deploy seja feito toda vez que nossa api for buildada
	- Em "Project"  selecione o projeto o qual sua api está
	- Em "Source (build pipeline)" selecione sua API
	- Clique em "Add"
- Nesse momento já configuramos qual o artefato que queremos fazer o deploy, porém devemos fazer com que cada build de um trigger para entregar no ambiente que desejamos
- Para isso, clique no botão que tem um simbolo de raio na caixa de "Artifacts"
- Habilite o primeiro campo "Continuous deployment trigger" e feche o popup
- Agora vamos configurar nosso deplay para homologação, clique em um link na segunda caixa (Stages) que estará escrito algo como "1 job, 1 task" e preencha conforme abaixo:
	- Stage Name -> Nome a seu criterio para seu stage ("caixinha")
	- Azure Subscription -> selecione o "Resource Manager" que conectamos nos passos anteriores
	- App Type -> Selecione "Web App for Conteiners (Linux)"
	- App Service Name -> Selecione o nome que criou para sua API no passo anterior
	- Registry or Namespace -> informe a URL do seu registro de conteiner, no meu caso é "carlosazregistry.azurecr.io"
	- Repository -  Informe o nome do repositório criado na etapa de build, no meu caso é "azcarlosapitempo-microservice-release" (essa informação esta no seu arquivo azure-pipelines.yml)
- No lado esquerdo, clique em "Run on Agent"
	- Geralmente nada precisa ser mudadao aqui, porém como estamos usando um self hosted Agent pool, no campo Agent Pool, devemos selecionar o Pool que informamos no nosso arquivo "azure-pipelines.yml" que no nosso exemplo é o "Carlos Pipeline"
- Novamente no lado esquerdo, clique em "Deploy Azure App Service"
	- se fizemos tudo certo nas etapas anteriores, nada precisa ser feito aqui, só mesmo conferir os valores
- Clique no botão "Save" próximo ao canto superior direito
- Quando aparecer um popup, clique em "OK"
- No top da tela, tem um texto co o valor de "New release pipeline", vamos trocar esse texto para o nome do nosso projeto, no nosso exemplo, "TempoApi"
- Clique no botão "Save" próximo ao canto superior direito
- Quando aparecer um popup, clique em "OK"
- Prontinho, nossa esteira de CD está criada, agora basta subirmos alguma alteração na nossa branch de release para que tudo funcione automagicamente

--------------------------------------------------------------------------------------
Testando sua Esteira
--------------------------------------------------------------------------------------
Após criar a pipiline de build e de deploy, agora vamos testar se esta tudo funcionando conforme planejado, para isso vamos criar um arquivo qualquer e subir no nosso repositório
	- Entre na pasta onde a solution da sua api esta localizada
	- Clique com o botão direito e selecione o item "Git Bash Here"
	- assim que o terminal abrir, vamos executar os seguintes comandos
		- git checkout release (para garantir que estamos na branch correta)
		- git pull (para garantir que estamos com a ultima versão do código)
		- touch dummy.txt
		- git add .
		- git commit -m "Inclusão de arquivo dummy"
		- git push origin release
- Agora se você acessar https://dev.azure.com/, selecionarr seu projeto e selecionar o menu Pipelines, verá que seu build já está rodando
- Aguarde até ele terminar (ficar co um botão verde) e selecione o sub-menu releases
- Você verá que o deploy da sua aplicação está sendo feito
- Assim que terminar, acesse https://portal.azure.com/
- Busque por serviçoes de aplicativos
- Clique no seu aplicativo
- Quando a tela carregar, copie o valor que esta no campo URL, cole no seu navegador acrescentando o caminho "/weatherforecast"
	- no meu caso ficou "https://azcarlosapitempo.azurewebsites.net/weatherforecast"
- Pronto, sua API esta configurada para entregar suas modificações de forma totalmente automatizada
--------------------------------------------------------------------------------------
CORS
--------------------------------------------------------------------------------------
- Entrar no https:www.google.com
- F12 e no console tentar chamar a api
- await fetch('https://testeapi-microservice.azurewebsites.net/weatherforecast')
- Deverá dar erro de CORS

Liberar Cors

- Acessar o web app criado
- No lado esquerdo da tela procurar o item CORS
- Abaixo de "Origens Permitidas" cadastrar todas as origens desejadas
- Checar o campo "Habilitar Acesso..."
- Clicar em Salvar

Testando liberação CORS

- Entrar no https:www.google.com
- CTRL+F5
- F12 e no console tentar chamar a api
- await fetch('https://azcarlosapitempo.azurewebsites.net/weatherforecast')
- await (await fetch('https://azcarlosapitempo.azurewebsites.net/weatherforecast')).json()

--------------------------------------------------------------------------------------
Links Uteis
--------------------------------------------------------------------------------------
- Erro: No hosted parallelism has been purchased or granted)
https://devblogs.microsoft.com/devops/change-in-azure-pipelines-grant-for-private-projects/
https://aka.ms/azpipelines-parallelism-request

- Agent Configuration
https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/v2-windows?view=azure-devops

- Agents self hosted
https://docs.microsoft.com/pt-br/azure/devops/pipelines/agents/agents?view=azure-devops&tabs=browser

- Registry enable
https://docs.microsoft.com/en-us/azure/container-registry/container-registry-authentication#admin-account

- WSL atualização
https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi
--------------------------------------------------------------------------------------
