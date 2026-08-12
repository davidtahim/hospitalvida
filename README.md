# Hospital Vida+

## 1. Introdução

O presente projeto foi desenvolvido como atividade prática de Banco de Dados Relacionais, com foco na modelagem, criação e otimização de um banco de dados para o ambiente hospitalar do sistema **Hospital Vida+**.

A proposta consiste em estruturar uma base inicial para atender operações básicas de cadastro de pacientes, médicos e consultas, além de aplicar conceitos de abstração, análise de performance e modelagem de dados para um cenário realista de fisioterapia.

---

## 2. Objetivo Geral

O objetivo deste trabalho é criar um banco de dados relacional em MySQL Workbench, organizado em três etapas principais:

- Fase 1: estrutura inicial e carga de dados;
- Fase 2: abstração e otimização;
- Fase 3: solução do desafio final da fisioterapia.

Dessa forma, o projeto demonstra a aplicação prática de conceitos como integridade referencial, chave primária, chave estrangeira, consultas filtradas, uso de `EXPLAIN`, criação de índices e modelagem de extensões para novos requisitos.

---

## 3. Banco de Dados

O banco principal a ser criado é:

```sql
CREATE DATABASE hospital_grupo01;
USE hospital_grupo01;
```

---

## 4. Estrutura Inicial do Banco

### 4.1 Tabela paciente
A tabela `paciente` armazena os dados cadastrais dos pacientes atendidos no hospital.

```sql
CREATE TABLE paciente (
    id_paciente INT NOT NULL AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    cpf VARCHAR(11) NOT NULL,
    data_nascimento DATE NOT NULL,
    PRIMARY KEY (id_paciente),
    UNIQUE (cpf)
);
```

### 4.2 Tabela medico
A tabela `medico` registra os profissionais de saúde vinculados ao sistema.

```sql
CREATE TABLE medico (
    id_medico INT NOT NULL AUTO_INCREMENT,
    nome VARCHAR(100) NOT NULL,
    crm VARCHAR(20) NOT NULL,
    PRIMARY KEY (id_medico),
    UNIQUE (crm)
);
```

### 4.3 Tabela consulta
A tabela `consulta` relaciona pacientes e médicos a partir das consultas realizadas.

```sql
CREATE TABLE consulta (
    id_consulta INT NOT NULL AUTO_INCREMENT,
    id_paciente INT NOT NULL,
    id_medico INT NOT NULL,
    data_hora DATETIME NOT NULL,
    PRIMARY KEY (id_consulta),
    CONSTRAINT fk_consulta_paciente
        FOREIGN KEY (id_paciente) REFERENCES paciente(id_paciente),
    CONSTRAINT fk_consulta_medico
        FOREIGN KEY (id_medico) REFERENCES medico(id_medico)
);
```

---

## 5. Fase 1 — Estrutura Inicial e Carga de Dados

### 5.1 Cadastro de pacientes
Os dados iniciais dos pacientes são inseridos na tabela `paciente`.

```sql
INSERT INTO paciente (nome, cpf, data_nascimento) VALUES
    ('Ana Lima', '12345678901', '1990-05-14'),
    ('Bruno Costa', '23456789012', '1985-09-22'),
    ('Carla Souza', '34567890123', '1978-12-03');
```

### 5.2 Cadastro de médicos
Os profissionais cadastrados no sistema são inseridos na tabela `medico`.

```sql
INSERT INTO medico (nome, crm) VALUES
    ('Dra. Helena Mendes', 'CRM-SP-12345'),
    ('Dr. Rafael Torres', 'CRM-SP-67890'),
    ('Dra. Beatriz Nunes', 'CRM-RJ-45678');
```

### 5.3 Cadastro de consultas
As consultas realizadas são registradas na tabela `consulta` vinculando paciente e médico.

```sql
INSERT INTO consulta (id_paciente, id_medico, data_hora) VALUES
    (1, 1, '2026-01-10 09:00:00'),
    (2, 2, '2026-01-11 14:30:00'),
    (3, 3, '2026-01-12 11:15:00');
```

---

## 6. Fase 2 — Abstração e Otimização

### 6.1 Consulta filtrada por CPF
A busca por pacientes pode ser realizada por meio do CPF, conforme a seguinte consulta:

```sql
SELECT *
FROM paciente
WHERE cpf = '12345678901';
```

### 6.2 Análise de desempenho com EXPLAIN
Para avaliar a execução da consulta e compreender o plano de execução, utiliza-se `EXPLAIN`.

```sql
EXPLAIN SELECT *
FROM paciente
WHERE cpf = '12345678901';
```

### 6.3 Criação de índice
Com o objetivo de melhorar o desempenho da busca por CPF, foi criado o índice `idx_paciente_cpf`:

```sql
CREATE INDEX idx_paciente_cpf
ON paciente (cpf);
```

### 6.4 Verificação dos índices
A consulta abaixo permite verificar os índices presentes na tabela `paciente`:

```sql
SHOW INDEX FROM paciente;
```

Essa etapa demonstra a importância dos índices em consultas frequentes e a sua contribuição para a otimização do banco de dados.

---

## 7. Fase 3 — Desafio Final: Fisioterapia

### 7.1 Alternativa C — entidade própria para avaliações fisioterapêuticas
Para atender ao requisito da fisioterapia, foi criada uma tabela específica para armazenar avaliações respiratórias dos pacientes.

```sql
CREATE TABLE avaliacao_fisioterapeutica (
    id_avaliacao INT NOT NULL AUTO_INCREMENT,
    id_paciente INT NOT NULL,
    data_avaliacao DATE NOT NULL,
    avaliacao_respiratoria VARCHAR(255) NOT NULL,
    PRIMARY KEY (id_avaliacao),
    CONSTRAINT fk_avaliacao_paciente
        FOREIGN KEY (id_paciente) REFERENCES paciente(id_paciente)
);
```

### 7.2 Inserção de dados de teste
Foram inseridas duas avaliações em datas diferentes para o mesmo paciente, conforme exemplo do roteiro.

```sql
INSERT INTO avaliacao_fisioterapeutica (id_paciente, data_avaliacao, avaliacao_respiratoria) VALUES
    (1, '2026-02-05', 'Paciente apresenta esforço respiratório leve, com melhora da capacidade ventilatória em repouso.'),
    (1, '2026-03-15', 'Paciente mantém melhora parcial, porém ainda apresenta desconforto respiratório ao caminhar por alguns minutos.');
```

### 7.3 Consulta do histórico de avaliações
Abaixo está a consulta que busca o histórico de avaliações de um paciente específico:

```sql
SELECT p.id_paciente,
       p.nome,
       p.cpf,
       a.id_avaliacao,
       a.data_avaliacao,
       a.avaliacao_respiratoria
FROM paciente p
INNER JOIN avaliacao_fisioterapeutica a
    ON p.id_paciente = a.id_paciente
WHERE p.id_paciente = 1
ORDER BY a.data_avaliacao;
```

Essa consulta permite acompanhar o histórico clínico de um paciente de forma organizada e estruturada, respeitando a separação das informações em entidades específicas.

---

## 8. Considerações Finais

Este projeto representa uma aplicação prática de modelagem relacional no contexto hospitalar, abordando aspectos fundamentais da administração de dados em sistemas de saúde. O banco foi estruturado de forma a garantir integridade, facilitar consultas, melhorar performance com índices e atender necessidades específicas de áreas como a fisioterapia.

Além disso, a atividade permite compreender a importância de:

- organização de dados;
- uso adequado de constraints;
- relacionamento entre tabelas;
- análise de desempenho;
- expansão funcional do modelo sem perder a consistência.

---

## 9. Conclusão

A implementação do sistema do Hospital Vida+ demonstra corretamente os conceitos trabalhados em Banco de Dados Relacionais. A estrutura inicial do banco, a otimização de consultas e a solução do desafio da fisioterapia foram organizadas de maneira coerente e funcional, atendendo ao roteiro da atividade prática e reforçando o aprendizado sobre modelagem e gestão de dados em ambiente hospitalar.
