# 🛒 Sistema de Loja e Pedidos — Banco de Dados

Projeto desenvolvido para a disciplina de **Banco de Dados**, com o objetivo de transformar um **Modelo Entidade-Relacionamento (MER)** em um **Modelo Físico Relacional**, utilizando **PostgreSQL** e comandos SQL.

O sistema representa o controle de **clientes, produtos, pedidos e itens de pedidos** de uma loja.

---

## 📚 Sobre o projeto

O projeto foi desenvolvido a partir de um MER contendo as entidades relacionadas ao gerenciamento de uma loja.

A implementação utiliza comandos **DDL** e **DML** do SQL para:

* Criar as tabelas do sistema;
* Definir chaves primárias;
* Definir chaves estrangeiras;
* Aplicar restrições de integridade;
* Inserir dados de teste;
* Realizar consultas utilizando `SELECT`;
* Trabalhar com filtros, ordenação e funções de agregação.

O banco de dados utilizado é o **PostgreSQL**, conforme especificado na atividade.

---

## 🗂️ Estrutura do banco

O banco é composto por quatro tabelas principais:

```text
cliente
   │
   │ 1:N
   ▼
pedido
   │
   │ 1:N
   ▼
itemPedido
   ▲
   │ N:1
   │
produto
```

### Tabelas

#### 👤 `cliente`

Armazena os dados dos clientes.

| Campo            | Tipo         | Descrição                  |
| ---------------- | ------------ | -------------------------- |
| `codigo_cliente` | SERIAL       | Chave primária             |
| `email`          | VARCHAR(120) | E-mail do cliente          |
| `nome`           | VARCHAR(100) | Nome do cliente            |
| `telefone`       | VARCHAR(13)  | Telefone, podendo ser nulo |

---

#### 📦 `produto`

Armazena os produtos disponíveis na loja.

| Campo            | Tipo          | Descrição         |
| ---------------- | ------------- | ----------------- |
| `codigo_produto` | SERIAL        | Chave primária    |
| `nome`           | VARCHAR(100)  | Nome do produto   |
| `preco`          | NUMERIC(10,2) | Preço do produto  |
| `unidade_medida` | VARCHAR(5)    | Unidade de medida |

Exemplos de unidades utilizadas:

```text
UN
KG
L
CX
```

---

#### 🧾 `pedido`

Armazena os pedidos realizados pelos clientes.

| Campo               | Tipo          | Descrição                        |
| ------------------- | ------------- | -------------------------------- |
| `codigo_pedido`     | SERIAL        | Chave primária                   |
| `codigo_cliente_fk` | INTEGER       | Chave estrangeira para `cliente` |
| `dt_entrada`        | DATE          | Data de entrada do pedido        |
| `dt_embarque`       | DATE          | Data de embarque                 |
| `tipo`              | VARCHAR(40)   | Tipo do pedido                   |
| `desconto`          | NUMERIC(10,2) | Desconto aplicado                |
| `valor_total`       | NUMERIC(10,2) | Valor total do pedido            |

A coluna `codigo_cliente_fk` estabelece o relacionamento entre **cliente** e **pedido**.

---

#### 🛍️ `itemPedido`

Representa os produtos pertencentes a cada pedido.

| Campo           | Tipo          | Descrição                        |
| --------------- | ------------- | -------------------------------- |
| `id_pedido_fk`  | INTEGER       | Chave estrangeira para `pedido`  |
| `id_produto_fk` | INTEGER       | Chave estrangeira para `produto` |
| `quantidade`    | INTEGER       | Quantidade do produto            |
| `sub_total`     | NUMERIC(10,2) | Subtotal do item                 |

A tabela possui uma **chave primária composta**:

```sql
PRIMARY KEY (id_pedido_fk, id_produto_fk)
```

Essa restrição impede que o mesmo produto seja repetido no mesmo pedido.

---

## 🔗 Relacionamentos

O banco utiliza **chaves estrangeiras (`FOREIGN KEY`)** para representar os relacionamentos definidos no modelo.

### Cliente → Pedido

Um cliente pode possuir vários pedidos.

```text
cliente 1 ───────── N pedido
```

Implementado através de:

```sql
codigo_cliente_fk INTEGER NOT NULL
REFERENCES cliente(codigo_cliente)
```

### Pedido → Produto

Um pedido pode possuir vários produtos e um produto pode aparecer em vários pedidos.

Esse relacionamento **N:N** é resolvido pela tabela associativa `itemPedido`.

```text
pedido 1 ───────── N itemPedido N ───────── 1 produto
```

---

## 🔐 Restrições de integridade

O projeto utiliza diferentes restrições para manter a consistência dos dados.

### Chaves primárias

Todas as tabelas possuem uma chave primária.

```sql
PRIMARY KEY
```

### Chaves estrangeiras

Os relacionamentos são implementados utilizando:

```sql
REFERENCES
```

### Campos obrigatórios

Os campos obrigatórios utilizam:

```sql
NOT NULL
```

### Valores positivos

Os preços, quantidades e valores possuem restrições utilizando `CHECK`.

Exemplo:

```sql
CHECK (preco > 0)
```

### Desconto

O desconto possui valor padrão `0` e não permite valores negativos:

```sql
DEFAULT 0
CHECK (desconto >= 0)
```

---

## 🧪 Dados cadastrados

O projeto possui dados de teste inseridos utilizando `INSERT`.

### Clientes

Foram cadastrados **5 clientes**, incluindo clientes com e sem telefone.

### Produtos

Foram cadastrados **8 produtos**, utilizando diferentes unidades de medida:

* `UN`
* `KG`
* `L`
* `CX`

### Pedidos

Foram cadastrados **6 pedidos**, incluindo:

* Pedidos com desconto;
* Pedidos sem desconto;
* Pedidos já embarcados;
* Pedidos ainda não embarcados;
* Pedidos realizados em datas diferentes.

### Itens

Foram cadastrados **12 itens de pedidos**.

---

## 🔎 Consultas SQL

O projeto contém consultas utilizando `SELECT`, sem utilização de `JOIN`, conforme a proposta da atividade.

Entre as consultas implementadas estão:

* Listagem de clientes;
* Nome e telefone dos clientes;
* Clientes em ordem alfabética;
* Busca de clientes pelo início do nome;
* Busca por clientes contendo "Silva";
* Clientes sem telefone;
* Listagem de produtos;
* Nome e preço dos produtos;
* Produtos ordenados por preço;
* Produtos acima de determinado valor;
* Produtos dentro de uma faixa de preço;
* Produtos por unidade de medida;
* Listagem de pedidos;
* Pedidos com desconto;
* Pedidos embarcados;
* Pedidos não embarcados;
* Pedidos entre determinadas datas;
* Pedidos ordenados pelo valor;
* Pedidos acima de R$ 500,00;
* Itens com determinada quantidade;
* Itens com subtotal elevado;
* Contagem de clientes;
* Contagem de produtos;
* Maior preço;
* Menor preço;
* Média dos preços;
* Soma dos valores dos pedidos;
* Contagem de pedidos não embarcados.

---

## 📊 Funções de agregação utilizadas

O projeto também utiliza funções de agregação do PostgreSQL:

```sql
COUNT()
MAX()
MIN()
AVG()
SUM()
```

Exemplos:

```sql
SELECT COUNT(*) FROM cliente;
```

```sql
SELECT MAX(preco) FROM produto;
```

```sql
SELECT AVG(preco) FROM produto;
```

```sql
SELECT SUM(valor_total) FROM pedido;
```

---

## 🛠️ Tecnologias utilizadas

| Tecnologia     | Utilização                                               |
| -------------- | -------------------------------------------------------- |
| **PostgreSQL** | Sistema gerenciador de banco de dados                    |
| **SQL**        | Linguagem utilizada para criação e manipulação dos dados |
| **pgAdmin**    | Ferramenta para execução dos comandos SQL                |

---

## 📁 Estrutura do repositório

```text
projeto-banco-dados/
│
├── README.md
│
└── felipe_carvalho_pereira_modelo_fisico_sql.sql
```

---

## ▶️ Como executar

### 1. Instale o PostgreSQL

Instale o PostgreSQL e o pgAdmin.

### 2. Abra o Query Tool

No pgAdmin, abra o **Query Tool**.

### 3. Crie o banco de dados

O banco definido pela atividade é:

```text
loja_pedidos
```

### 4. Execute o arquivo SQL

Abra o arquivo:

```text
felipe_carvalho_pereira_modelo_fisico_sql.sql
```

e execute os comandos no PostgreSQL.

### 5. Consulte as tabelas

Depois da execução, as tabelas principais serão:

```text
cliente
produto
pedido
itemPedido
```

---

## 🎯 Objetivos acadêmicos

Este projeto tem como objetivo praticar conceitos fundamentais de **Banco de Dados Relacionais**, especialmente:

* Modelagem de dados;
* Conversão de MER para modelo relacional;
* DDL;
* DML;
* `CREATE TABLE`;
* `INSERT`;
* `SELECT`;
* Chaves primárias;
* Chaves estrangeiras;
* Restrições de integridade;
* `NOT NULL`;
* `CHECK`;
* `DEFAULT`;
* Ordenação;
* Filtros;
* Funções de agregação;
* Relacionamentos 1:N;
* Relacionamentos N:N;
* Entidades associativas.

---

## 📌 Status do projeto

**Em desenvolvimento / atividade acadêmica.**

A implementação atual contém:

* [x] Estrutura das tabelas
* [x] Chaves primárias
* [x] Chaves estrangeiras
* [x] Restrições de integridade
* [x] Cadastro de clientes
* [x] Cadastro de produtos
* [x] Cadastro de pedidos
* [x] Cadastro de itens
* [x] Consultas `SELECT`
* [x] Funções de agregação
* [ ] Operações `UPDATE`
* [ ] Operações `DELETE`
* [ ] Criação do banco `loja_pedidos` diretamente no script

---

## 👨‍💻 Autor

**Felipe Carvalho Pereira**

Projeto acadêmico desenvolvido para estudo de **Banco de Dados e SQL**.
