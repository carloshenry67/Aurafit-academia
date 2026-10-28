AuraFit — Sistema de Gestão de Academia
Projeto acadêmico de banco de dados relacional para organizar a operação da academia fictícia AuraFit, desde o cadastro de alunos até o controle de planos, matrículas, faturas, instrutores, fichas de treino, exercícios e modalidades preferidas.
SGBD: MySQL
Modelagem: MySQL Workbench
Banco de dados: fitmanager_db

📌 Sumário
- 1. Domínio do banco de dados
- 2. Contextualização
- 3. Proposta de melhoria
- 4. Modelagem lógica
- 5. Relacionamentos e cardinalidades
- 6. Formas normais
- 7. Requisitos e implementação
- 8. Consultas SQL
- 9. Testes
- 10. Exportação e restauração do dump
- 11. Como executar o projeto
- 12. Estrutura sugerida do repositório
- 13. Entrega no Classroom
1. Domínio do banco de dados
O domínio escolhido é a gestão da AuraFit, uma academia fictícia. O banco de dados organiza informações sobre:
- alunos;
- telefones dos alunos;
- planos oferecidos pela academia;
- matrículas e seus status;
- faturas e pagamentos;
- instrutores;
- fichas de treino;
- exercícios;
- itens que compõem cada ficha de treino;
- modalidades preferidas pelos alunos.
O objetivo é centralizar esses dados e permitir consultas confiáveis sobre matrículas, inadimplência, planos contratados, treinos e interesses dos alunos.
2. Contextualização
Sem um SGBD relacional, a academia poderia manter os registros em planilhas, documentos e anotações manuais. Esse método pode causar:
- Duplicação de dados: os dados de um mesmo aluno podem aparecer em vários arquivos.
- Inconsistência: uma alteração pode ser realizada em um arquivo e não nos demais.
- Consultas demoradas: localizar matrículas, faturas e informações de treino exige trabalho manual.
- Falhas de integridade: podem existir registros associados a alunos, instrutores ou planos inexistentes.
- Dificuldade financeira: identificar faturas pendentes e somar os pagamentos recebidos pode ser trabalhoso.
- Problemas de acesso simultâneo: alterações feitas por funcionários diferentes podem causar sobrescrita ou perda de informações.
- Manutenção complexa: o crescimento da quantidade de alunos torna mais difícil organizar os dados.
3. Proposta de melhoria
A solução proposta é utilizar o MySQL para centralizar os dados em tabelas relacionadas. As chaves primárias identificam os registros, enquanto as chaves estrangeiras estabelecem vínculos entre as tabelas e ajudam a manter a integridade referencial.
Benefícios
- Centralização das informações;
- redução de redundâncias e inconsistências;
- facilidade para cadastrar, atualizar e consultar dados;
- controle do histórico de matrículas e faturas;
- relatórios financeiros por meio de SQL;
- organização dos treinos e exercícios;
- melhor suporte ao acesso simultâneo;
- possibilidade de gerar cópias de segurança e restaurar o banco.
4. Modelagem lógica
O projeto utiliza 11 tabelas.
4.1. alunos
Armazena os dados cadastrais dos alunos.
Campo	Tipo	Restrições/finalidade
id_aluno	INT	Chave primária, auto incremento
nome	VARCHAR(100)	Nome do aluno; obrigatório
cpf	VARCHAR(11)	CPF sem pontuação; único e obrigatório
data_nascimento	DATE	Data de nascimento; obrigatória
data_cadastro	DATE	Data de cadastro; obrigatória


4.2. telefones_aluno
Armazena os telefones de contato dos alunos.
Campo	Tipo	Restrições/finalidade
id_telefone	INT	Chave primária, auto incremento
id_aluno	INT	Chave estrangeira para alunos.id_aluno
numero	VARCHAR(15)	Número de telefone; obrigatório


4.3. instrutores
Armazena os dados dos instrutores da academia.
Campo	Tipo	Restrições/finalidade
id_instrutor	INT	Chave primária, auto incremento
nome	VARCHAR(100)	Nome do instrutor; obrigatório
cref	VARCHAR(15)	Registro profissional; único e obrigatório
especialidade	VARCHAR(50)	Área de especialidade


4.4. planos
Registra os planos oferecidos pela academia.
Campo	Tipo	Restrições/finalidade
id_plano	INT	Chave primária, auto incremento
nome_plano	VARCHAR(50)	Nome do plano; obrigatório
valor_mensal	DECIMAL(10,2)	Valor mensal; obrigatório
duracao_meses	INT	Duração do plano em meses; obrigatória


4.5. matriculas
Registra as matrículas e o vínculo do aluno com o plano contratado.
Campo	Tipo	Restrições/finalidade
id_matricula	INT	Chave primária, auto incremento
id_aluno	INT	Chave estrangeira para alunos.id_aluno
id_plano	INT	Chave estrangeira para planos.id_plano
data_inicio	DATE	Data de início da matrícula
status	VARCHAR(20)	Situação da matrícula, por exemplo Ativa ou Inativa


4.6. faturas
Armazena as cobranças relacionadas às matrículas.
Campo	Tipo	Restrições/finalidade
id_fatura	INT	Chave primária, auto incremento
id_matricula	INT	Chave estrangeira para matriculas.id_matricula
data_vencimento	DATE	Data de vencimento
data_pagamento	DATE	Data do pagamento; pode ser nula
valor	DECIMAL(10,2)	Valor da fatura
status_pagamento	VARCHAR(20)	Situação, por exemplo Pago ou Pendente


4.7. fichas_treino
Armazena as fichas de treino elaboradas para os alunos.
Campo	Tipo	Restrições/finalidade
id_treino	INT	Chave primária, auto incremento
id_aluno	INT	Chave estrangeira para alunos.id_aluno
id_instrutor	INT	Chave estrangeira para instrutores.id_instrutor
objetivo	VARCHAR(100)	Objetivo do treino
data_criacao	DATE	Data de criação da ficha


4.8. exercicios
Mantém o catálogo de exercícios disponíveis.
Campo	Tipo	Restrições/finalidade
id_exercicio	INT	Chave primária, auto incremento
nome_exercicio	VARCHAR(100)	Nome do exercício
grupo_muscular	VARCHAR(50)	Grupo muscular trabalhado


4.9. itens_treino
Relaciona os exercícios com as fichas de treino e registra suas configurações.
Campo	Tipo	Restrições/finalidade
id_treino	INT	Parte da chave primária composta e FK para fichas_treino.id_treino
id_exercicio	INT	Parte da chave primária composta e FK para exercicios.id_exercicio
series	INT	Quantidade de séries
repeticoes	INT	Quantidade de repetições
carga_kg	DECIMAL(5,2)	Carga em quilogramas; pode ser nula


A chave primária composta é (id_treino, id_exercicio).
4.10. modalidades
Armazena as modalidades disponíveis ou de interesse dos alunos.
Campo	Tipo	Restrições/finalidade
id_modalidade	INT	Chave primária, auto incremento
nome_modalidade	VARCHAR(50)	Nome da modalidade


4.11. aluno_modalidade_preferida
Tabela associativa que registra as modalidades preferidas pelos alunos.
Campo	Tipo	Restrições/finalidade
id_aluno	INT	Parte da chave primária composta e FK para alunos.id_aluno
id_modalidade	INT	Parte da chave primária composta e FK para modalidades.id_modalidade


A chave primária composta é (id_aluno, id_modalidade).
5. Relacionamentos e cardinalidades
Relacionamento	Cardinalidade	Explicação
alunos → telefones_aluno	1:N	Um aluno pode ter vários telefones; cada telefone pertence a um aluno.
alunos → matriculas	1:N	Um aluno pode ter várias matrículas ao longo do tempo; cada matrícula pertence a um aluno.
planos → matriculas	1:N	Um plano pode estar associado a várias matrículas.
matriculas → faturas	1:N	Uma matrícula pode possuir várias faturas.
alunos → fichas_treino	1:N	Um aluno pode possuir várias fichas de treino.
instrutores → fichas_treino	1:N	Um instrutor pode elaborar várias fichas.
fichas_treino ↔ exercicios	N:M	Uma ficha contém vários exercícios e um exercício pode estar em várias fichas. A tabela itens_treino resolve o relacionamento.
alunos ↔ modalidades	N:M	Um aluno pode preferir várias modalidades e uma modalidade pode ser preferida por vários alunos. A tabela aluno_modalidade_preferida resolve o relacionamento.


Não existe um relacionamento 1:1 explícito no modelo atual.
Diagrama
O diagrama lógico deve ser incluído no repositório como imagem exportada do MySQL Workbench.

Imagem da modelagem: docs/modelagem.png

Salve o print da modelagem na pasta docs/ com o nome modelagem.png. Caso utilize outro nome ou caminho, ajuste o endereço da imagem acima.

6. Formas normais
A normalização organiza as tabelas para reduzir redundâncias e evitar anomalias de inserção, atualização e exclusão.
1FN — Primeira Forma Normal
Cada campo deve armazenar um único valor, sem listas ou grupos repetidos. No projeto, os telefones são armazenados individualmente em telefones_aluno, em vez de registrar vários telefones em uma única coluna de alunos.
2FN — Segunda Forma Normal
A tabela deve estar na 1FN e todos os atributos não-chave devem depender da chave primária completa. Em itens_treino, os atributos series, repeticoes e carga_kg descrevem um exercício dentro de uma ficha específica e dependem da combinação de id_treino e id_exercicio. A tabela aluno_modalidade_preferida também utiliza uma chave composta.
3FN — Terceira Forma Normal
A tabela deve estar na 2FN e os atributos não-chave não devem depender transitivamente da chave primária. Por exemplo, faturas armazena id_matricula, sem repetir o id_aluno ou o id_plano. Essas informações podem ser obtidas por meio da tabela matriculas, evitando redundância.
4FN — Quarta Forma Normal
A 4FN trata dependências multivaloradas independentes que podem gerar redundância. O projeto mantém os telefones em telefones_aluno e as preferências em aluno_modalidade_preferida, separando diferentes conjuntos de informações do aluno. A confirmação formal da 4FN depende da análise das dependências multivaloradas presentes no modelo.
7. Requisitos e implementação
Para executar o projeto, são necessários:
- servidor MySQL;
- MySQL Workbench (recomendado para executar os scripts e visualizar a modelagem);
- arquivo SQL com criação das tabelas, dados de teste e consultas.
O banco deve se chamar fitmanager_db e conter as 11 tabelas descritas neste documento.
Dados de demonstração
O script de dados deve inserir no mínimo cinco registros em cada uma das 11 tabelas. As tabelas associativas e de telefones também precisam respeitar essa quantidade mínima.

Confira as quantidades depois de executar o script, usando a consulta da seção 9. Testes.
8. Consultas SQL
O projeto prevê 15 consultas contextualizadas, demonstrando recursos como:
- INNER JOIN e LEFT JOIN;
- WHERE e LIKE;
- BETWEEN;
- GROUP BY e ORDER BY;
- SUM, COUNT e AVG;
- subconsultas e COALESCE.
8.1. Listar alunos e telefones — INNER JOIN
SELECT a.nome, t.numero AS telefone
FROM alunos AS a
INNER JOIN telefones_aluno AS t ON t.id_aluno = a.id_aluno;
Resposta esperada: exibe o nome dos alunos que possuem telefone cadastrado e seus respectivos números.
8.2. Alunos cadastrados a partir de fevereiro de 2025 — WHERE
SELECT nome, cpf, data_cadastro
FROM alunos
WHERE data_cadastro >= '2025-02-01'
ORDER BY data_cadastro;
Resposta com os dados de exemplo: Lucas Mendes, Fernanda Costa e Roberto Alves.
8.3. Buscar aluno pelo sobrenome Silva — LIKE
SELECT id_aluno, nome
FROM alunos
WHERE nome LIKE '%Silva%';
Resposta com os dados de exemplo: Carlos Silva.
8.4. Faturas em um intervalo de vencimento — BETWEEN
SELECT id_fatura, valor, data_vencimento, status_pagamento
FROM faturas
WHERE data_vencimento BETWEEN '2025-02-01' AND '2025-03-15'
ORDER BY data_vencimento;
Resposta esperada: lista as faturas com vencimento entre 1º de fevereiro e 15 de março de 2025, incluindo as datas-limite.
8.5. Alunos com matrícula ativa — INNER JOIN
SELECT a.nome AS aluno, p.nome_plano, m.data_inicio
FROM matriculas AS m
INNER JOIN alunos AS a ON a.id_aluno = m.id_aluno
INNER JOIN planos AS p ON p.id_plano = m.id_plano
WHERE m.status = 'Ativa'
ORDER BY a.nome;
Resposta esperada: mostra os alunos com matrícula ativa, o plano contratado e a data de início.
8.6. Faturamento total de faturas pagas — SUM
SELECT COALESCE(SUM(valor), 0) AS faturamento_total
FROM faturas
WHERE status_pagamento = 'Pago';
Resposta com os dados de exemplo: R$ 340,00.
8.7. Quantidade de matrículas por plano — GROUP BY e COUNT
SELECT p.nome_plano, COUNT(m.id_matricula) AS total_matriculas
FROM planos AS p
LEFT JOIN matriculas AS m ON m.id_plano = p.id_plano
GROUP BY p.id_plano, p.nome_plano
ORDER BY total_matriculas DESC, p.nome_plano;
Resposta esperada: mostra a quantidade de matrículas associadas a cada plano, inclusive os planos sem matrículas.
8.8. Planos com valor mensal acima da média — AVG
SELECT nome_plano, valor_mensal
FROM planos
WHERE valor_mensal > (SELECT AVG(valor_mensal) FROM planos)
ORDER BY valor_mensal DESC;
Resposta com os dados de exemplo: Mensal Bronze e Trimestral Prata.
8.9. Ordenar alunos por data de nascimento — ORDER BY
SELECT nome, data_nascimento
FROM alunos
ORDER BY data_nascimento DESC;
Resposta esperada: lista os alunos do nascimento mais recente para o mais antigo.
8.10. Fichas de treino com aluno e instrutor — INNER JOIN
SELECT ft.id_treino, a.nome AS aluno, i.nome AS instrutor,
       ft.objetivo, ft.data_criacao
FROM fichas_treino AS ft
INNER JOIN alunos AS a ON a.id_aluno = ft.id_aluno
INNER JOIN instrutores AS i ON i.id_instrutor = ft.id_instrutor
ORDER BY ft.data_criacao;
Resposta esperada: exibe o objetivo e a data de criação de cada ficha, com o aluno e o instrutor associados.
8.11. Exercícios com carga superior a 25 kg — WHERE e JOIN
SELECT ft.id_treino, e.nome_exercicio, it.series,
       it.repeticoes, it.carga_kg
FROM itens_treino AS it
INNER JOIN exercicios AS e ON e.id_exercicio = it.id_exercicio
INNER JOIN fichas_treino AS ft ON ft.id_treino = it.id_treino
WHERE it.carga_kg > 25.00
ORDER BY it.carga_kg DESC;
Resposta com os dados de exemplo: Supino Reto com Barra, Puxada Frontal e Leg Press 45.
8.12. Quantidade e valor de faturas pendentes — COUNT e SUM
SELECT COUNT(*) AS quantidade_pendente,
       COALESCE(SUM(valor), 0) AS total_pendente
FROM faturas
WHERE status_pagamento = 'Pendente';
Resposta com os dados de exemplo: uma fatura pendente, totalizando R$ 120,00.
8.13. Modalidades preferidas pelos alunos — GROUP BY
SELECT mo.nome_modalidade,
       COUNT(amp.id_aluno) AS total_interessados
FROM modalidades AS mo
LEFT JOIN aluno_modalidade_preferida AS amp
       ON amp.id_modalidade = mo.id_modalidade
GROUP BY mo.id_modalidade, mo.nome_modalidade
ORDER BY total_interessados DESC, mo.nome_modalidade;
Resposta esperada: lista as modalidades por quantidade de alunos interessados, incluindo modalidades sem preferências registradas.
8.14. Faturas pendentes e telefone para contato — LEFT JOIN
SELECT a.nome, t.numero AS telefone,
       f.valor, f.data_vencimento
FROM faturas AS f
INNER JOIN matriculas AS m ON m.id_matricula = f.id_matricula
INNER JOIN alunos AS a ON a.id_aluno = m.id_aluno
LEFT JOIN telefones_aluno AS t ON t.id_aluno = a.id_aluno
WHERE f.status_pagamento = 'Pendente'
ORDER BY f.data_vencimento;
Resposta com os dados de exemplo: identifica o aluno vinculado à fatura pendente e apresenta o telefone, se houver um cadastrado.
8.15. Média de carga por grupo muscular — AVG
SELECT e.grupo_muscular,
       ROUND(AVG(it.carga_kg), 2) AS media_carga_kg
FROM itens_treino AS it
INNER JOIN exercicios AS e ON e.id_exercicio = it.id_exercicio
GROUP BY e.grupo_muscular
ORDER BY media_carga_kg DESC;
Resposta esperada: apresenta a média das cargas registradas para cada grupo muscular.
Os resultados dependem dos registros inseridos. Execute as consultas no MySQL Workbench e confirme os valores antes de utilizar os resultados em uma apresentação ou relatório.

9. Testes
Após executar o script, verifique se as tabelas foram criadas:
USE fitmanager_db;
SHOW TABLES;
Confira a quantidade de registros de cada tabela:
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
Verifique também:
- se as 11 tabelas foram criadas;
- se todas possuem pelo menos cinco registros;
- se as chaves primárias e estrangeiras funcionam;
- se as 15 consultas são executadas sem erros;
- se os relacionamentos retornam os dados esperados;
- se uma fatura pode ficar sem data de pagamento quando ainda estiver pendente;
- se o dump pode ser restaurado em um banco de teste.
O uso de IF NOT EXISTS evita erros ao criar objetos existentes, mas não atualiza automaticamente uma estrutura antiga nem impede que os mesmos INSERTs sejam executados novamente. Para repetir os testes, utilize uma base limpa ou prepare uma rotina de reinicialização.

10. Exportação e restauração do dump
Um dump é um arquivo que contém a estrutura e os dados do banco e pode ser usado para backup e restauração.
A — MySQL Workbench
1. Abra o MySQL Workbench e conecte-se ao servidor.
2. Acesse Server → Data Export.
3. Selecione o schema fitmanager_db.
4. Marque as tabelas que deseja exportar.
5. Escolha Dump Structure and Data.
6. Selecione Export to Self-Contained File.
7. Defina o nome fitmanager_db_dump.sql.
8. Clique em Start Export e confirme que o arquivo foi criado.
B — Terminal
Para exportar a estrutura e os dados:
mysqldump -u seu_usuario -p --databases fitmanager_db > fitmanager_db_dump.sql
Digite a senha quando solicitada e substitua seu_usuario pelo usuário configurado no MySQL.

Para restaurar o dump:
mysql -u seu_usuario -p < fitmanager_db_dump.sql
Depois da restauração, confira as tabelas, a quantidade de registros e a execução das consultas. O comando mysqldump inclui estrutura e dados por padrão; não utilize opções como --no-data se precisar entregar ambos.
11. Como executar o projeto
1. Clone ou baixe o repositório.
2. Inicie o MySQL Server.
3. Abra o MySQL Workbench e conecte-se ao servidor.
4. Abra o script SQL do projeto.
5. Execute o script para criar o banco, as tabelas e inserir os dados.
6. Execute a consulta de verificação da seção de testes.
7. Execute as 15 consultas e confira os resultados.
8. Gere um dump com a estrutura e os dados.
9. Abra o arquivo de modelagem no MySQL Workbench ou consulte a imagem incluída no repositório.
Também é possível executar o script pelo terminal, caso esteja salvo em sql/aurafit.sql:
