Escrevi no medium sobre o fluxo de criação de esteira de CI/CD na Azure utilizando docker:

[Criando uma esteira de CI/CD com Docker na Azure do zero — Parte 1](https://karlos-oliveira.medium.com/criando-uma-esteira-de-ci-cd-com-docker-na-azure-do-zero-parte-1-bf0d5c31d546)

[Criando uma esteira de CI/CD com Docker na Azure do zero — Parte 2](https://karlos-oliveira.medium.com/criando-uma-esteira-de-ci-cd-com-docker-na-azure-do-zero-parte-2-8480e28a2aea)

--------------------------------------------------------------------------------------
CORS
--------------------------------------------------------------------------------------
- Entrar no https://www.google.com
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

- Entrar no https://www.google.com
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
