# Projeto CRUD com Docker Compose

Este projeto foi desenvolvido como parte das atividades da faculdade, com o objetivo de colocar em prática conceitos de **Docker, Docker Compose e DevOps**.

Criamos uma aplicação web simples para cadastrar e gerenciar produtos, utilizando **PHP e MySQL**. Também configuramos containers para executar o sistema e um workflow no **GitHub Actions** para realizar o deploy em um servidor com runner *self-hosted*.

## Equipe

- **Fabrício da Silva**
- **Gustavo Oliveira Alves**

## Sobre o sistema

A aplicação permite realizar as quatro operações de um CRUD (*Create, Read, Update e Delete*):

- **Cadastrar:** adicionar um produto com nome, descrição e quantidade em estoque.
- **Listar:** visualizar os produtos cadastrados.
- **Editar:** alterar as informações de um produto.
- **Excluir:** remover um produto cadastrado.

Os dados são armazenados no banco MySQL. A tabela `produto` é criada automaticamente pela aplicação quando a conexão é estabelecida, caso ainda não exista.

## Estrutura do repositório

```text
.
├── .github/
│   └── workflows/
│       └── deploy.yml       # Workflow de deploy pelo GitHub Actions
├── src2/
│   ├── conexao.php          # Conexão e criação da tabela no MySQL
│   ├── criar.php            # Cadastro de produtos
│   ├── editar.php           # Edição de produtos
│   ├── excluir.php          # Exclusão de produtos
│   └── index.php            # Listagem dos produtos
├── docker-compose.yml      # Configuração dos containers
└── README.md               # Documentação do projeto
```

## Docker Compose

Utilizamos o **Docker Compose** para executar os serviços necessários sem precisar instalar PHP, Apache ou MySQL diretamente na máquina.

O arquivo `docker-compose.yml` configura três containers:

| Serviço | Função | Acesso |
|---|---|---|
| **PHP + Apache** | Executa a aplicação web. | `http://localhost:8080` |
| **MySQL** | Armazena os produtos cadastrados. | Porta `3306` |
| **phpMyAdmin** | Permite visualizar e administrar o banco de dados pelo navegador. | `http://localhost:8081` |

Os containers se comunicam pela rede `minha-rede`. Também configuramos o volume `mysql_data`, que mantém os dados do banco mesmo quando os containers são interrompidos e recriados, desde que o volume não seja removido.

## Como executar o projeto

Antes de começar, é necessário ter **Docker** e **Docker Compose** instalados e funcionando.

**1. Clone o repositório:**

```bash
git clone https://github.com/ffabricioo779/CRUD-Compose-Docker.git
cd CRUD-Compose-Docker
```

**2. Inicie os containers:**

```bash
docker compose up -d
```

**3. Acesse o sistema no navegador:**

- Aplicação: [http://localhost:8080](http://localhost:8080)
- phpMyAdmin: [http://localhost:8081](http://localhost:8081)

Para acessar o phpMyAdmin, utilize o servidor `mysql` e as credenciais de desenvolvimento configuradas no arquivo `docker-compose.yml`.

**4. Para parar e remover os containers:**

```bash
docker compose down
```

Se também quiser apagar os dados armazenados no volume do banco, utilize `docker compose down -v`. **Atenção:** esse último comando remove os dados persistidos.

## Automação implementada (GitHub Actions)

Além dos containers, criamos um workflow chamado **Deploy**, localizado em `.github/workflows/deploy.yml`.

| Workflow | Quando executa | O que faz |
|---|---|---|
| **Deploy** | Manualmente, pela aba **Actions** do GitHub | Executa comandos Docker em um runner *self-hosted* para reiniciar e atualizar os containers. |

### Deploy com runner self-hosted

Configuramos um runner *self-hosted* para que o GitHub Actions execute comandos diretamente em uma máquina preparada para hospedar a aplicação.

O workflow utiliza os seguintes comandos:

```bash
docker compose down
docker compose up -d --build
```

O primeiro comando encerra e remove os containers da aplicação. O segundo inicia novamente os serviços definidos no Docker Compose.

### Como executar o deploy

1. Acesse o repositório no GitHub.
2. Abra a aba **Actions**.
3. Selecione o workflow **Deploy**.
4. Clique em **Run workflow** e confirme a execução.
5. Acompanhe os resultados na página de execução do workflow.

Para funcionar, o runner precisa estar online, com Docker e Docker Compose disponíveis, além de possuir permissão para utilizar o Docker.

## Histórico de atividades realizadas

- Criação do repositório e desenvolvimento do CRUD de produtos.
- Configuração da conexão entre PHP e MySQL.
- Criação dos containers da aplicação, banco de dados e phpMyAdmin.
- Configuração da rede interna e do volume de persistência.
- Execução da aplicação com Docker Compose.
- Configuração do runner *self-hosted*.
- Criação do workflow de deploy no GitHub Actions.
- Organização da documentação do projeto.

## Tecnologias e ferramentas

- **PHP:** desenvolvimento da aplicação.
- **HTML e Bootstrap:** estrutura e aparência das páginas.
- **MySQL:** armazenamento dos produtos.
- **phpMyAdmin:** administração visual do banco de dados.
- **Docker:** execução dos serviços em containers.
- **Docker Compose:** organização dos containers, da rede e do volume.
- **Git e GitHub:** versionamento e compartilhamento do projeto.
- **GitHub Actions:** execução do workflow de deploy.
- **Runner self-hosted:** execução do workflow na máquina configurada.
