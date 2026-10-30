# Projeto CRUD com Docker Compose

Este projeto foi desenvolvido como parte das atividades da faculdade, com o objetivo de colocar em prática conceitos de Docker, Docker Compose e DevOps.

Criamos uma aplicação web para cadastrar e gerenciar produtos, utilizando PHP e MySQL. Também configuramos um Runner self-hosted do GitHub Actions para realizar o deploy automático em uma máquina virtual Ubuntu Server.

## Equipe

- **Fabrício da Silva** — RA: 250285
- **Gustavo Oliveira Alves** — RA: 250274

## Sobre o sistema

A aplicação permite realizar as quatro operações de um CRUD (Create, Read, Update e Delete):

- **Cadastrar:** adicionar produtos com nome, descrição e quantidade em estoque.
- **Listar:** visualizar os produtos cadastrados.
- **Editar:** alterar as informações dos produtos.
- **Excluir:** remover produtos cadastrados.

Os dados são armazenados no banco MySQL. A tabela `produto` é criada automaticamente pela aplicação, caso ainda não exista.

## Estrutura do repositório

```text
.
├── .github/
│   └── workflows/
│       └── deploy.yml
├── src2/
│   ├── conexao.php
│   ├── criar.php
│   ├── editar.php
│   ├── excluir.php
│   └── index.php
├── docker-compose.yml
└── README.md
```

## Tecnologias utilizadas

- **PHP:** desenvolvimento do CRUD.
- **HTML e Bootstrap:** estrutura e aparência das páginas.
- **MySQL:** armazenamento dos produtos.
- **phpMyAdmin:** gerenciamento do banco de dados.
- **Docker:** execução da aplicação em contêineres.
- **Docker Compose:** gerenciamento dos serviços.
- **Ubuntu Server:** sistema operacional da máquina virtual.
- **Git e GitHub:** versionamento e armazenamento do código.
- **GitHub Actions:** automação do deploy.
- **Runner self-hosted:** execução dos comandos diretamente na VM.

## Docker Compose

O projeto utiliza Docker Compose para executar três serviços:

| Serviço | Função | Acesso |
|---|---|---|
| PHP + Apache | Executa a aplicação CRUD | Porta 8080 |
| MySQL | Armazena os produtos | Porta 3306 |
| phpMyAdmin | Gerencia o banco de dados | Porta 8081 |

Os contêineres se comunicam pela rede `minha-rede`. O volume `mysql_data` mantém os dados do banco mesmo quando os contêineres são recriados, desde que o volume seja preservado.

## Como executar o projeto

É necessário ter Git, Docker e Docker Compose instalados.

**1. Clonar o repositório:**

```bash
git clone https://github.com/ffabricioo779/CRUD-Compose-Docker.git
cd CRUD-Compose-Docker
```

**2. Iniciar os contêineres:**

```bash
sudo docker compose up -d --build
```

**3. Verificar os contêineres:**

```bash
sudo docker compose ps
```

**4. Parar os contêineres:**

```bash
sudo docker compose down
```

Esse último comando não remove o volume persistente do banco de dados.

## Máquina virtual Ubuntu Server

A aplicação está hospedada em uma máquina virtual Ubuntu Server, utilizando Docker e Docker Compose.

A máquina virtual também possui um Runner self-hosted configurado para executar o workflow do GitHub Actions.

### Acesso à aplicação

**IP utilizado na VM:** `172.16.9.135`

- **Aplicação CRUD:** http://172.16.9.135:8080
- **phpMyAdmin:** http://172.16.9.135:8081

O acesso depende de a VM estar ligada, dos serviços estarem funcionando e de o computador possuir conexão com a rede da máquina virtual.

Para consultar o IP da VM:

```bash
hostname -I
```

O endereço pode mudar conforme a configuração da rede.

## Automação com GitHub Actions

Criamos um workflow chamado **Deploy**, localizado em `.github/workflows/deploy.yml`.

Sua função é atualizar automaticamente a aplicação na VM sempre que uma alteração é enviada para a branch `main` do GitHub.

### Como funciona o Runner self-hosted

O Runner self-hosted é um programa instalado e registrado na máquina virtual.

Ele recebe os trabalhos enviados pelo GitHub Actions e executa os comandos diretamente na VM.

Assim, quando fazemos uma alteração no GitHub, o Runner executa a Action e atualiza a aplicação utilizando Docker Compose.

O Runner precisa estar online e ter permissão para utilizar o Docker.

## Explicação do deploy.yml

### 1. Execução automática

```yaml
# Nome da automação
name: Deploy

# Executa automaticamente após um push na main
on:
  push:
    branches:
      - main

  # Permite executar manualmente
  workflow_dispatch:
```

Esse trecho faz com que a Action seja iniciada automaticamente quando uma alteração é enviada para a branch `main`.

### 2. Controle de execuções

```yaml
# Evita dois deploys simultâneos
concurrency:
  group: deploy-vm
  cancel-in-progress: false
```

Organiza as execuções para que dois deploys não ocorram ao mesmo tempo.

### 3. Runner self-hosted

```yaml
# Define o trabalho de deploy
jobs:
  deploy:

    # Executa no Runner instalado na VM
    runs-on: self-hosted
```

Esse trecho indica que o workflow será executado pelo Runner local.

### 4. Atualização do código

```yaml
# Baixa o código atualizado do GitHub
- name: Checkout
  uses: actions/checkout@v4
```

O Checkout disponibiliza os arquivos atualizados do repositório para a execução do workflow.

### 5. Execução do Docker

```yaml
# Etapa responsável por atualizar a aplicação
- name: Docker
  run: |
    # Mostra uma mensagem indicando o início
    echo "Iniciando o deploy..."

    # Atualiza e inicia os contêineres do projeto
    docker compose up -d --build

    # Mostra os contêineres em execução
    docker compose ps

    # Mostra uma mensagem indicando a conclusão
    echo "Deploy realizado com sucesso!"
```

**Explicação dos comandos:**

- `name: Docker`: identifica a etapa responsável pelo Docker.
- `run: |`: permite executar vários comandos no terminal.
- `echo "Iniciando o deploy..."`: exibe uma mensagem de início.
- `docker compose up -d --build`: constrói e inicia os serviços, atualizando os contêineres quando necessário.
- `docker compose ps`: mostra o estado dos contêineres.
- `echo "Deploy realizado com sucesso!"`: exibe uma mensagem quando os comandos terminam sem erros.

**Resumo:** essa etapa utiliza Docker Compose para atualizar a aplicação na máquina virtual após uma alteração enviada ao GitHub.

## Como funciona o deploy automático

O processo acontece da seguinte maneira:

1. Um integrante altera algum arquivo do projeto.
2. A alteração é enviada para a branch `main` do GitHub.
3. O GitHub Actions inicia automaticamente o workflow Deploy.
4. O Runner self-hosted recebe e executa o trabalho na VM.
5. O Checkout disponibiliza o código atualizado.
6. O Docker Compose atualiza os contêineres da aplicação.
7. A nova versão fica disponível no navegador.

Dessa forma, não é necessário realizar o deploy manualmente após cada alteração.

Também é possível iniciar manualmente pela aba **Actions → Deploy → Run workflow**.

## Teste do deploy automático

Para comprovar o funcionamento da automação, realizamos uma alteração no título principal da aplicação.

**Antes:**

`Produtos`

**Depois:**

`Produtos - Teste Deploy Automático`

A alteração foi realizada diretamente no arquivo `src2/index.php` pelo GitHub.

Após salvar o commit na branch `main`, o GitHub Actions iniciou automaticamente o workflow Deploy.

**Informações do teste:**

- **Execução:** Deploy #7
- **Commit:** `79cc95f`
- **Branch:** `main`
- **Acionamento:** push
- **Duração:** 16 segundos
- **Resultado:** sucesso

Depois da execução, acessamos a aplicação pelo navegador utilizando o endereço:

http://172.16.9.135:8080

O novo título apareceu corretamente na página, comprovando que a alteração foi aplicada na máquina virtual.

## Histórico de atividades realizadas

- Criação do repositório no GitHub.
- Desenvolvimento do CRUD de produtos.
- Configuração do PHP e MySQL.
- Configuração do Docker e Docker Compose.
- Preparação da máquina virtual Ubuntu Server.
- Instalação e configuração do Runner self-hosted.
- Criação do workflow de deploy.
- Configuração da execução automática após push.
- Realização do teste de atualização da aplicação.
- Verificação do resultado pelo navegador.
- Organização da documentação do projeto.

## Conclusão

Com este trabalho, colocamos em prática conceitos de DevOps, utilizando GitHub Actions, Docker Compose e uma máquina virtual Ubuntu Server.

Conseguimos executar uma aplicação CRUD e configurar um Runner self-hosted para atualizar automaticamente o sistema após alterações enviadas ao GitHub.

O teste realizado demonstrou o funcionamento do deploy automático, permitindo compreender a integração entre GitHub Actions, Runner local e Docker Compose.
