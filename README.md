# AuraFit — Banco de Dados para Academia

Projeto acadêmico de banco de dados relacional para gerenciar alunos, planos, matrículas, pagamentos, instrutores e treinos de uma academia fictícia.

**SGBD:** MySQL  
**Banco de dados:** `fitmanager_db`

---

## 📌 Sumário

1. [Domínio](#1-domínio)
2. [Contextualização](#2-contextualização)
3. [Proposta de melhoria](#3-proposta-de-melhoria)
4. [Modelagem lógica](#4-modelagem-lógica)
5. [Formas normais](#5-formas-normais)
6. [Criação e dados SQL](#6-criação-e-dados-sql)
7. [Consultas SQL](#7-consultas-sql)
8. [Testes](#8-testes)
9. [Dump do banco](#9-dump-do-banco)
10. [Estrutura do repositório](#10-estrutura-do-repositório)
11. [Entrega](#11-entrega)

---

## 1. Domínio

O projeto trata da gestão de uma academia, centralizando informações sobre:

- Alunos e seus telefones;
- Planos e matrículas;
- Faturas e pagamentos;
- Instrutores e fichas de treino;
- Exercícios e modalidades preferidas.

## 2. Contextualização

O uso de planilhas e anotações pode causar duplicação de dados, informações desatualizadas, dificuldade para localizar pagamentos pendentes e demora na criação de relatórios.

## 3. Proposta de melhoria

Utilizar o MySQL para centralizar as informações, relacionar tabelas por meio de chaves primárias e estrangeiras, reduzir inconsistências e facilitar consultas e backups.

## 4. Modelagem lógica

O banco de dados possui **11 tabelas**.

### 4.1. Tabelas e campos

| Tabela | Campos e tipos de dados |
|---|---|
| `alunos` | `id_aluno INT` (PK), `nome VARCHAR(100)`, `cpf VARCHAR(11)`, `data_nascimento DATE`, `data_cadastro DATE` |
| `telefones_aluno` | `id_telefone INT` (PK), `id_aluno INT` (FK), `numero VARCHAR(15)` |
| `instrutores` | `id_instrutor INT` (PK), `nome VARCHAR(100)`, `cref VARCHAR(15)`, `especialidade VARCHAR(50)` |
| `planos` | `id_plano INT` (PK), `nome_plano VARCHAR(50)`, `valor_mensal DECIMAL(10,2)`, `duracao_meses INT` |
| `matriculas` | `id_matricula INT` (PK), `id_aluno INT` (FK), `id_plano INT` (FK), `data_inicio DATE`, `status VARCHAR(20)` |
| `faturas` | `id_fatura INT` (PK), `id_matricula INT` (FK), `data_vencimento DATE`, `data_pagamento DATE`, `valor DECIMAL(10,2)`, `status_pagamento VARCHAR(20)` |
| `fichas_treino` | `id_treino INT` (PK), `id_aluno INT` (FK), `id_instrutor INT` (FK), `objetivo VARCHAR(100)`, `data_criacao DATE` |
| `exercicios` | `id_exercicio INT` (PK), `nome_exercicio VARCHAR(100)`, `grupo_muscular VARCHAR(50)` |
| `itens_treino` | `id_treino INT` (PK/FK), `id_exercicio INT` (PK/FK), `series INT`, `repeticoes INT`, `carga_kg DECIMAL(5,2)` |
| `modalidades` | `id_modalidade INT` (PK), `nome_modalidade VARCHAR(50)` |
| `aluno_modalidade_preferida` | `id_aluno INT` (PK/FK), `id_modalidade INT` (PK/FK) |

**Legenda:**

- **PK:** chave primária.
- **FK:** chave estrangeira.
- **PK/FK:** campo que participa da chave primária composta e também é chave estrangeira.

### 4.2. Relacionamentos e cardinalidades

| Relacionamento | Cardinalidade | Descrição |
|---|---|---|
| `alunos` → `telefones_aluno` | 1:N | Um aluno pode ter vários telefones. |
| `alunos` → `matriculas` | 1:N | Um aluno pode ter várias matrículas ao longo do tempo. |
| `planos` → `matriculas` | 1:N | Um plano pode estar associado a várias matrículas. |
| `matriculas` → `faturas` | 1:N | Uma matrícula pode possuir várias faturas. |
| `alunos` → `fichas_treino` | 1:N | Um aluno pode ter várias fichas de treino. |
| `instrutores` → `fichas_treino` | 1:N | Um instrutor pode elaborar várias fichas. |
| `fichas_treino` ↔ `exercicios` | N:M | Uma ficha contém vários exercícios, e um exercício pode estar em várias fichas. |
| `alunos` ↔ `modalidades` | N:M | Um aluno pode preferir várias modalidades, e uma modalidade pode ser preferida por vários alunos. |

Os relacionamentos N:M são implementados por meio das tabelas associativas `itens_treino` e `aluno_modalidade_preferida`.

### 4.3. Diagrama da modelagem

Salve a imagem do diagrama lógico na pasta `docs/`, com o nome `modelagem.png`.

![Diagrama lógico do banco de dados AuraFit](docs/modelagem.png)

## 5. Formas normais

- **1FN — Primeira Forma Normal:** cada campo armazena um único valor. Por isso, os telefones são separados na tabela `telefones_aluno`.
- **2FN — Segunda Forma Normal:** os atributos devem depender da chave primária completa. Em `itens_treino`, séries, repetições e carga dependem da combinação entre treino e exercício.
- **3FN — Terceira Forma Normal:** evita dependências transitivas e repetição desnecessária de informações. Por exemplo, `faturas` referencia `matriculas`, sem repetir os dados do aluno e do plano.
- **4FN — Quarta Forma Normal:** separa conjuntos de informações multivaloradas independentes. No projeto, telefones e modalidades preferidas são armazenados em tabelas próprias.

## 6. Criação e dados SQL

O script `sql/aurafit.sql` deve criar o banco, as 11 tabelas e inserir **pelo menos cinco registros em cada tabela**, respeitando as chaves estrangeiras.

### Criação do banco

```sql
CREATE DATABASE IF NOT EXISTS fitmanager_db
    CHARACTER SET utf8mb4
    COLLATE utf8mb4_unicode_ci;

USE fitmanager_db;
```

A estrutura completa das tabelas e os comandos `INSERT` devem estar no script SQL do repositório.

## 7. Consultas SQL

O projeto deve conter **15 consultas contextualizadas**, com seus resultados conferidos. As consultas devem demonstrar o uso de `INNER JOIN`, `LEFT JOIN`, `WHERE`, `LIKE`, `BETWEEN`, `GROUP BY`, `ORDER BY`, `SUM`, `COUNT` e `AVG`.

### Exemplos de consultas

**1. Listar alunos e seus telefones**

```sql
SELECT a.nome, t.numero
FROM alunos AS a
INNER JOIN telefones_aluno AS t
    ON t.id_aluno = a.id_aluno;
```

**2. Buscar alunos pelo nome**

```sql
SELECT nome
FROM alunos
WHERE nome LIKE '%Silva%';
```

**3. Somar as faturas pagas**

```sql
SELECT COALESCE(SUM(valor), 0) AS faturamento
FROM faturas
WHERE status_pagamento = 'Pago';
```

**4. Contar matrículas por plano**

```sql
SELECT
    p.nome_plano,
    COUNT(m.id_matricula) AS total
FROM planos AS p
LEFT JOIN matriculas AS m
    ON m.id_plano = p.id_plano
GROUP BY p.id_plano, p.nome_plano
ORDER BY total DESC;
```

Os resultados dependem dos dados cadastrados. Execute as consultas no MySQL Workbench para conferir as respostas corretas.

## 8. Testes

Para verificar se o banco e as tabelas foram criados, execute:

```sql
USE fitmanager_db;

SHOW TABLES;
```

Para verificar a quantidade de registros em cada tabela:

```sql
SELECT 'alunos' AS tabela, COUNT(*) AS total
FROM alunos

UNION ALL

SELECT 'telefones_aluno', COUNT(*)
FROM telefones_aluno

UNION ALL

SELECT 'instrutores', COUNT(*)
FROM instrutores

UNION ALL

SELECT 'planos', COUNT(*)
FROM planos

UNION ALL

SELECT 'matriculas', COUNT(*)
FROM matriculas

UNION ALL

SELECT 'faturas', COUNT(*)
FROM faturas

UNION ALL

SELECT 'fichas_treino', COUNT(*)
FROM fichas_treino

UNION ALL

SELECT 'exercicios', COUNT(*)
FROM exercicios

UNION ALL

SELECT 'itens_treino', COUNT(*)
FROM itens_treino

UNION ALL

SELECT 'modalidades', COUNT(*)
FROM modalidades

UNION ALL

SELECT 'aluno_modalidade_preferida', COUNT(*)
FROM aluno_modalidade_preferida;
```

Todas as tabelas devem apresentar pelo menos cinco registros. Também é necessário testar as chaves estrangeiras e executar as 15 consultas.

## 9. Dump do banco

O **dump** é um arquivo que contém a estrutura e os dados do banco, permitindo realizar backups e restaurações.

### 9.1. Pelo terminal

Para exportar o banco:

```bash
mysqldump -u seu_usuario -p --databases fitmanager_db > fitmanager_db_dump.sql
```

Para restaurar o arquivo:

```bash
mysql -u seu_usuario -p < fitmanager_db_dump.sql
```

Substitua `seu_usuario` pelo usuário configurado no MySQL.

### 9.2. Pelo MySQL Workbench

1. Abra o MySQL Workbench e conecte-se ao servidor.
2. Acesse **Server → Data Export**.
3. Selecione o banco `fitmanager_db`.
4. Escolha **Dump Structure and Data**.
5. Selecione o arquivo de saída.
6. Clique em **Start Export**.

Após a exportação, teste a restauração para confirmar que a estrutura e os dados foram salvos corretamente.
