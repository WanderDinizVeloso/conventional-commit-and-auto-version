# Automatização de versão e versionamento semântico dos repositórios
Documentação criada visando a padronização de commits, através do versionamento semãntico e a automatizaçao das versões dos repositórios.

---

## Sumário

- [Entendendo o versionamento semântico](#entendendo-o-versionamento-semântico)
- [Padronização dos commits](#padronização-dos-commits)
	- [Tipos dos Commits, pela Conventional Commits](#tipos-dos-commits-pela-conventional-commits)
- [Ferramentas utilizadas](#ferramentas-utilizadas)
	- [Commitlint](#commitlint)
  - [Husky](#husky)
  - [Semantic-release](#semantic-release)

---

## Entendendo o versionamento semântico
O versionamento semântico aborda um conjunto simples de regras e requisitos que determinam como os números da versão são atribuídos e por sua vez, incrementados.

Dados um número de versão X.Y.Z, deve-se incrementá-las seguindo as seguintes regras:
 - X ou MAJOR: quando você realizar alterações incompatíveis da API;
 - Y ou MINOR: quando você adicionar funcionalidades compatíveis com versões anteriores;
 - Z ou PATCH: quando você corrigir erros compatíveis com versões anteriores.
 
Exemplo: version "1.8.2":
X ou MAJOR: 1
Y ou MINOR: 8
Z ou PATCH: 2

---

## Padronização dos commits
A Padronização dos commits visa:
 - Evitar commits muito vagos que não se explica a alteração;
 - Comunica a mudança feita na aplicação para as pessoas da equipe, e para qualquer outra pessoa interessada com objetividade e assertividade;
 - Descreve o motivo da alteração ou correção do código;
 - Facilita a contribuição de outras pessoas ao projeto.
 - Facilita o versionamento semântico e criação de CHANGELOG.

Para a automatização de versão utilizaremos a especificação [Conventional Commits](https://www.conventionalcommits.org/pt-br/v1.0.0/), onde é estruturada da seguinte maneira:
 ```bash
<tipo>[escopo opcional]: <descrição>

[corpo opcional]

[rodapé(s) opcional(is)]
 ```

Os ¨tipos" serão descritos mais a frente.

O "escopo" é a funcionalidade ou contexto da aplicação.

A "descrição" deve ser uma descrição sucinta da mudança.

O "corpo" é a descrição do que foi feito detalhadamente.

O "rodapé" é usado para links de referência como issues do gitHub, tikets do Jira, etc.

### Tipos dos Commits, pela Conventional Commits:
 - chore: Atualização de tarefas que não ocasionam alteração no código de produção, mas mudanças de ferramentas, mudanças de configuração e bibliotecas.
 - feat: São adições de novas funcionalidades ou de quaisquer outras novas implantações ao código.
 - fix: Essencialmente definem o tratamento de correções de bugs.
 - refactor: Utilizado em quaisquer mudanças que sejam executados no código, porém não alterem a funcionalidade final da tarefa impactada.
 - docs: Inclusão ou alteração somente de arquivos de documentação.
 - perf: Uma alteração de código que melhora o desempenho.
 - style: Alterações referentes a formatações na apresentação do código que não afetam o significado do código, como por exemplo: espaço em branco, formatação, ponto e vírgula ausente etc.
 - test: Adicionando testes ausentes ou corrigindo testes existentes nos processos de testes automatizados (TDD).
 - build: Alterações que afetam o sistema de construção ou dependências externas (escopos de exemplo: gulp, broccoli, npm).
 - ci: Mudanças em nossos arquivos e scripts de configuração de CI (exemplo de escopos: Travis, Circle, BrowserStack, SauceLabs).
 - env: Utilizado na descrição de modificações ou adições em arquivos de configuração em processos e métodos de integração contínua (CI), como parâmetros em arquivos de configuração de containers.
 - BREAKING CHANGE: um commit que indica que aquela alteração, muda a compatibilidade da versão do repositório e é representado pela na mensagem com "!" depois do type ou scope (ex. = "feat!: commit content").
 
Exemplos:
 - chore: add commitlint e husky
 - chore(eslint): obrigar o uso de aspas duplas no jsx
 - refactor: refatorando a tipagem
 - feat: add axios / buscando e tratando os dados
 - feat(page/home): criando o roteamentento no next

---

## Ferramentas utilizadas
### Commitlint
Pacote que visa verificar se a mensagem de commit que escrevemos realmente está dentro dos padrões pré definidos.

#### Instalação
 ```bash
# INSTALANDO O COMMITLINT
	npm i -D @commitlint/{cli,config-conventional}
 
# CRIANDO O COMMITLINT.CONFIG.JS
	echo "module.exports = { extends: ['@commitlint/config-conventional'] };" > commitlint.config.js
 ```
 Mais detalhes, na documentação: [commitlint](https://github.com/conventional-changelog/commitlint)

---

### Husky
O Husky executa ações que disparão em determinados momentos.
No nosso caso, vamos criar um gancho para ser disparado antes da criação de um commit.

#### Instalação
 ```bash
# INSTALANDO HUSKY e ATIVANDO OS HOOKS
	npx husky-init && npm install
	## OPCIONAL: por padrao, o husky habilita o test no precommit. Caso nao queira, é necessário excluir o arquivo './husky/precommit'
	
# ADICIONANDO A AÇÃO DO COMMITLINT NO HUSKY
	npx husky add .husky/commit-msg 'npx --no -- commitlint --edit "$1"'
 ```
Mais detalhes, na documentação: [Husky](https://typicode.github.io/husky/#/)

---

### Semantic-release
O Semantic-release é responsável por toda a automatização, incluindo:
 - o cálculo nova versao do repositório;
 - gera/atualiza o arquivo CHANGELOG;
 - cria as releases no github (caso utilize)
 - publica as alterações.
 
Ele é totalmente configurável, podendo determinar:
- Em qual configuração de padronização de commit será utilizada, como por exemplo: "Conventional Commits" ou "Angular";
- Em que momento será iniciada a mudança da versão, como por exemplo, ao mergear as alterações na "Master"/"Main";
- Entre outras configurações.

#### Instalação e configuração para atender ao Conventional Commit
 ```bash
# INSTALANDO O SEMANTIC-RELEASE
  npm i -D semantic-release
  
# INSTALANDO AS DEPENDÊNCIAS AUXILIARES
  npm i -D @semantic-release/changelog @semantic-release/git conventional-changelog-conventionalcommits
 ```
 
 Crie o arquivo ".releaserc" na raiz do repositório e configure, como no exemplo abaixo:
```bash
{
  "branches": ["main"],
  "baseBranch": "main",
  "tagFormat": "${version}",
  "preset": "conventionalcommits",
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/changelog",
    "@semantic-release/npm",
    "@semantic-release/github",
    "@semantic-release/git"
  ]
}
```

Para a nossa configuração, não publicaremos no NPM. Assim, é necessário adicionar a seguinte configuração no "package.json" do repositório:
```bash
  "private": true,
```
Obs.: Caso deseje que o Semantic-release publique automaticamente no NPM, acesse a documentação, para efetuar a configuração necessária. [semantic-release/npm](https://www.npmjs.com/package/@semantic-release/npm)

#### Configuração do Semantic-release no Github Actions

No repositório, caso não exista, crie o arquivo "release.yml" em .github/workflows

Configure o arquivo "release.yml", como no exemplo abaixo: 
 ```bash
name: Release

on:
  push:
    branches:
      - main

jobs:
  release:
    runs-on: "ubuntu-latest"
    env:
      GH_TOKEN: ${{ secrets.GH_TOKEN }}
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
          persist-credentials: false

      - uses: actions/setup-node@v3
        with:
          node-version: 18

      - name: Install Deps
        run: npm i -D @semantic-release/changelog @semantic-release/git conventional-changelog-conventionalcommits semantic-release

      - name: Semantic Release
        run: npx semantic-release --ci

 ```

#### Configurações necessárias no github

Para que o Semantic-release seja acionado no momento do merge das alterações do repositório, é necessário gerar um personal access token e configurá-la em seu repositório, com os seguintes passos:
 - Acesse sua conta do github e vá em settings > developer settings > personal access token
 - Crie um novo token em "Generate new token"
 - Escolha o nome do token em "Note", o tempo de expiração em "Expiration", selecione o escopo "workflow" em "Select scopes" e clique no botão "Generate token".
 - Copie o token gerado.
 - No repositório que receberá a configuração, vá em settings > Secrets > Actions
 - Crie um segredo em "New repository secret"
 - Coloque "GH_TOKEN" em "Name¨, cole o token gerado no campo "Value" e clique no botão "Add secret"

Pronto! Agora é só efetuar suas implementações no repositório e ao efetuar o merge do PR para a ¨Master"/"Main" o Semantic-release fará sua "mágica"!

Mais detalhes, na documentação: [Semantic-release](https://github.com/semantic-release/semantic-release)
