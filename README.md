AuraFit — Banco de Dados para Academia
Projeto acadêmico de banco de dados relacional para gerenciar alunos, planos, matrículas, pagamentos, instrutores e treinos de uma academia fictícia.

SGBD: MySQL
Banco: fitmanager_db
## 📌 Sumário

- [1. Domínio](#1-domínio)
- [2. Contextualização](#2-contextualização)
- [3. Proposta de melhoria](#3-proposta-de-melhoria)
- [4. Modelagem lógica](#4-modelagem-lógica)
- [5. Formas normais](#5-formas-normais)
- [6. Criação e dados SQL](#6-criação-e-dados-sql)
- [7. Consultas SQL](#7-consultas-sql)
- [8. Testes](#8-testes)
- [9. Dump do banco](#9-dump-do-banco)

1. Domínio
O projeto trata da gestão de uma academia, centralizando cadastros de alunos, telefones, planos, matrículas, faturas, instrutores, fichas de treino, exercícios e modalidades preferidas.
2. Contextualização
O uso de planilhas e anotações pode causar dados duplicados, informações desatualizadas, dificuldade para localizar pagamentos pendentes e demora na criação de relatórios.
3. Proposta de melhoria
Utilizar o MySQL para centralizar as informações, relacionar tabelas por chaves primárias e estrangeiras, reduzir inconsistências e facilitar consultas e backups.
4. Modelagem lógica
O banco possui 11 tabelas:
Tabela	Principais campos
alunos	id_aluno INT (PK), nome VARCHAR(100), cpf VARCHAR(11), data_nascimento DATE, data_cadastro DATE
telefones_aluno	id_telefone INT (PK), id_aluno INT (FK), numero VARCHAR(15)
instrutores	id_instrutor INT (PK), nome VARCHAR(100), cref VARCHAR(15), especialidade VARCHAR(50)
planos	id_plano INT (PK), nome_plano VARCHAR(50), valor_mensal DECIMAL(10,2), duracao_meses INT
matriculas	id_matricula INT (PK), id_aluno INT (FK), id_plano INT (FK), data_inicio DATE, status VARCHAR(20)
faturas	id_fatura INT (PK), id_matricula INT (FK), data_vencimento DATE, data_pagamento DATE, valor DECIMAL(10,2), status_pagamento VARCHAR(20)
fichas_treino	id_treino INT (PK), id_aluno INT (FK), id_instrutor INT (FK), objetivo VARCHAR(100), data_criacao DATE
exercicios	id_exercicio INT (PK), nome_exercicio VARCHAR(100), grupo_muscular VARCHAR(50)
itens_treino	id_treino INT (PK/FK), id_exercicio INT (PK/FK), series INT, repeticoes INT, carga_kg DECIMAL(5,2)
modalidades	id_modalidade INT (PK), nome_modalidade VARCHAR(50)
aluno_modalidade_preferida	id_aluno INT (PK/FK), id_modalidade INT (PK/FK)


PK: chave primária. FK: chave estrangeira.
Relacionamentos e cardinalidades
- alunos 1:N telefones_aluno
- alunos 1:N matriculas
- planos 1:N matriculas
- matriculas 1:N faturas
- alunos 1:N fichas_treino
- instrutores 1:N fichas_treino
- fichas_treino N:M exercicios, por meio de itens_treino
- alunos N:M modalidades, por meio de aluno_modalidade_preferida

Salve o print da modelagem em docs/modelagem.png para exibi-lo no GitHub.

5. Formas normais
- 1FN: cada campo contém um único valor; os telefones são separados em linhas na tabela telefones_aluno.
- 2FN: atributos das tabelas com chave composta dependem da chave completa, como em itens_treino.
- 3FN: dados de outras entidades não são repetidos sem necessidade; faturas referencia matriculas em vez de repetir aluno e plano.
- 4FN: informações multivaloradas independentes são separadas, como telefones e modalidades preferidas.
6. Criação e dados SQL
O script sql/aurafit.sql deve criar o banco, as 11 tabelas e inserir pelo menos cinco registros em cada tabela, respeitando as chaves estrangeiras.
CREATE DATABASE IF NOT EXISTS fitmanager_db
  CHARACTER SET utf8mb4
  COLLATE utf8mb4_unicode_ci;

USE fitmanager_db;
A estrutura completa, os INSERTs e as consultas devem ficar no script SQL do repositório.
7. Consultas SQL
O projeto deve conter 15 consultas contextualizadas, com seus resultados conferidos. Elas devem demonstrar INNER JOIN, LEFT JOIN, WHERE, LIKE, BETWEEN, GROUP BY, ORDER BY, SUM, COUNT e AVG.

Exemplos:
-- 1. Listar alunos e telefones
SELECT a.nome, t.numero
FROM alunos a
INNER JOIN telefones_aluno t ON t.id_aluno = a.id_aluno;

-- 2. Buscar alunos pelo nome
SELECT nome
FROM alunos
WHERE nome LIKE '%Silva%';

-- 3. Somar faturas pagas
SELECT COALESCE(SUM(valor), 0) AS faturamento
FROM faturas
WHERE status_pagamento = 'Pago';

-- 4. Contar matrículas por plano
SELECT p.nome_plano, COUNT(m.id_matricula) AS total
FROM planos p
LEFT JOIN matriculas m ON m.id_plano = p.id_plano
GROUP BY p.id_plano, p.nome_plano
ORDER BY total DESC;
Os resultados variam conforme os dados inseridos; execute as consultas no MySQL Workbench para registrar as respostas corretas.
8. Testes
Confira se as tabelas existem:
USE fitmanager_db;
SHOW TABLES;
Verifique se todas possuem ao menos cinco registros:
SELECT 'alunos' AS tabela, COUNT(*) AS total FROM alunos
UNION ALL SELECT 'telefones_aluno', COUNT(*) FROM telefones_aluno
UNION ALL SELECT 'instrutores', COUNT(*) FROM instrutores
UNION ALL SELECT 'planos', COUNT(*) FROM planos
UNION ALL SELECT 'matriculas', COUNT(*) FROM matriculas
UNION ALL SELECT 'faturas', COUNT(*) FROM faturas
UNION ALL SELECT 'fichas_treino', COUNT(*) FROM fichas_treino
UNION ALL SELECT 'exercicios', COUNT(*) FROM exercicios
UNION ALL SELECT 'itens_treino', COUNT(*) FROM itens_treino
UNION ALL SELECT 'modalidades', COUNT(*) FROM modalidades
UNION ALL SELECT 'aluno_modalidade_preferida', COUNT(*) FROM aluno_modalidade_preferida;
9. Dump do banco
O dump deve conter a estrutura e os dados.

Pelo terminal:
mysqldump -u seu_usuario -p --databases fitmanager_db > fitmanager_db_dump.sql
Para restaurar:
mysql -u seu_usuario -p < fitmanager_db_dump.sql
No MySQL Workbench, acesse Server → Data Export, selecione fitmanager_db, escolha Dump Structure and Data, defina o arquivo de saída e clique em Start Export.
