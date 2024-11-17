PORTFÓLIO

PROJETO

Os proprietários de uma faculdade precisam de um sistema que viabilize o
armazenamento de informações sobre seus alunos, cursos, matérias e professores para
que seja possível realizar controles básicos como montar turmas e realizar o
armazenamento de notas dos alunos.
Com base no que foi apresentado acima, o aluno deve criar um banco de dados que
ofereça suporte para que um sistema possa armazenar informações que atendam a
necessidade do cliente.
Para facilitar o desenvolvimento do projeto, identifique respostas para as seguintes
questões:
● Quais são as principais necessidades dos clientes?
a. Quais informações precisam ser armazenadas?
b. Quais os dados precisam ser guardados?
c. O que será feito com os dados posteriormente?
● Quais tabelas precisam ser criadas para que todas as informações sejam
armazenadas?
● Quais atributos cada tabela deve ter?
● Qual o tipo de dados de cada atributo definido?
● Quais são os relacionamentos a serem criados entre as tabelas?

LEVANTAMENTO DE REQUISITOS

1. Quais informações precisam ser armazenadas?
Informações sobre alunos (nome, data de nascimento, CPF, email, telefone, endereço).
Informações sobre cursos (nome do curso, carga horária, modalidade).
Informações sobre disciplinas (nome da disciplina, carga horária, pré-requisitos).
Informações sobre professores (nome, CPF, especialização, email).
Informações sobre turmas (código da turma, semestre, ano).
Notas dos alunos em disciplinas.

2. Quais os dados precisam ser guardados?
Dados de identificação dos alunos e professores.
Dados relacionados a cursos e disciplinas.
Informações sobre as turmas e as notas dos alunos.

3. O que será feito com os dados posteriormente?
Montar turmas e alocar alunos nas disciplinas.
Avaliar o desempenho dos alunos através das notas.
Gerar relatórios e estatísticas sobre cursos e alunos.

Tabelas a serem criadas:

Alunos
Cursos
Disciplinas
Professores
Turmas
Matrículas (associação entre alunos, turmas e disciplinas)
Notas

CREATE DATABASE db_sistema_faculdade;

USE db_sistema_faculdade;

#Tabela de endereços
CREATE TABLE tbl_enderecos (
    id_endereco INT PRIMARY KEY AUTO_INCREMENT, 
    logradouro VARCHAR(255) NOT NULL, 
    numero VARCHAR(10) NOT NULL, 
    bairro VARCHAR(100) NOT NULL, 
    cidade VARCHAR(100) NOT NULL, 
    estado VARCHAR(2) NOT NULL, 
    cep VARCHAR(10) NOT NULL
);

#Tabela de alunos
CREATE TABLE tbl_alunos (
    id_aluno INT PRIMARY KEY AUTO_INCREMENT, 
    nome VARCHAR(255) NOT NULL, 
    data_nascimento DATE NOT NULL, 
    cpf VARCHAR(14) UNIQUE NOT NULL, 
    email VARCHAR(255) UNIQUE NOT NULL, 
    senha VARCHAR(255) NOT NULL, 
    telefone VARCHAR(15) NOT NULL, 
    id_endereco INT NOT NULL,
    FOREIGN KEY (id_endereco) REFERENCES tbl_enderecos(id_endereco)
);

#Tabela de cursos
CREATE TABLE tbl_cursos (
    id_curso INT PRIMARY KEY AUTO_INCREMENT, 
    nome VARCHAR(255) NOT NULL, 
    carga_horaria INT NOT NULL, 
    modalidade VARCHAR(50) NOT NULL
);

#Tabela de turmas
CREATE TABLE tbl_turmas (
    id_turma INT PRIMARY KEY AUTO_INCREMENT, 
    nome VARCHAR(100) NOT NULL, 
    ano INT NOT NULL, 
    semestre VARCHAR(10) NOT NULL, 
    id_curso INT NOT NULL,
    FOREIGN KEY (id_curso) REFERENCES tbl_cursos(id_curso)
);

#Tabela de professores
CREATE TABLE tbl_professores (
    id_professor INT PRIMARY KEY AUTO_INCREMENT, 
    nome VARCHAR(255) NOT NULL, 
    cpf VARCHAR(14) UNIQUE NOT NULL, 
    email VARCHAR(255) UNIQUE NOT NULL, 
    especializacao VARCHAR(255), 
    telefone VARCHAR(15) NOT NULL,
    senha VARCHAR(255) NOT NULL
);

#Tabela de disciplinas
CREATE TABLE tbl_disciplinas (
    id_disciplina INT PRIMARY KEY AUTO_INCREMENT, 
    nome VARCHAR(255) NOT NULL, 
    carga_horaria INT NOT NULL, 
    pre_requisito INT,
    id_professor INT NOT NULL,
    FOREIGN KEY (pre_requisito) REFERENCES tbl_disciplinas(id_disciplina),
    FOREIGN KEY (id_professor) REFERENCES tbl_professores(id_professor)
);

#Tabela de matrículas
CREATE TABLE tbl_matriculas (
    id_matricula INT PRIMARY KEY AUTO_INCREMENT, 
    id_aluno INT NOT NULL, 
    id_turma INT NOT NULL, 
    id_disciplina INT NOT NULL,
    FOREIGN KEY (id_aluno) REFERENCES tbl_alunos(id_aluno),
    FOREIGN KEY (id_turma) REFERENCES tbl_turmas(id_turma),
    FOREIGN KEY (id_disciplina) REFERENCES tbl_disciplinas(id_disciplina)
);

#Tabela de avaliações
CREATE TABLE tbl_avaliacoes (
    id_avaliacao INT PRIMARY KEY AUTO_INCREMENT, 
    nota FLOAT NOT NULL, 
    id_professor INT NOT NULL, 
    id_matricula INT NOT NULL,
    FOREIGN KEY (id_professor) REFERENCES tbl_professores(id_professor),
    FOREIGN KEY (id_matricula) REFERENCES tbl_matriculas(id_matricula)
);

SHOW TABLES;

# VISÃO GERAL DO ALUNO
SELECT 
    a.nome AS aluno, 
    a.email, 
    a.telefone, 
    e.logradouro, 
    e.numero, 
    e.bairro, 
    e.cidade, 
    e.estado, 
    e.cep
FROM tbl_alunos a
JOIN tbl_enderecos e ON a.id_endereco = e.id_endereco;


# TURMAS E CURSOS
SELECT 
    t.nome AS turma, 
    t.ano, 
    t.semestre, 
    c.nome AS curso, 
    c.modalidade, 
    p.nome AS professor
FROM tbl_turmas t
JOIN tbl_cursos c ON t.id_curso = c.id_curso
JOIN tbl_disciplinas d ON d.id_disciplina = t.id_turma -- Associando turma com disciplinas
JOIN tbl_professores p ON d.id_professor = p.id_professor;


# ALUNOS MATRICULADOS EM TURMAS E SEUS CURSOS
SELECT 
    a.nome AS aluno, 
    t.nome AS turma, 
    c.nome AS curso 
FROM tbl_matriculas m
JOIN tbl_alunos a ON m.id_aluno = a.id_aluno
JOIN tbl_turmas t ON m.id_turma = t.id_turma
JOIN tbl_cursos c ON t.id_curso = c.id_curso;


# DISCIPLINAS E SEUS PROFESSORES
SELECT 
    d.nome AS disciplina, 
    d.carga_horaria, 
    p.nome AS professor, 
    p.especializacao, 
    pr.nome AS pre_requisito 
FROM tbl_disciplinas d
JOIN tbl_professores p ON d.id_professor = p.id_professor
LEFT JOIN tbl_disciplinas pr ON d.pre_requisito = pr.id_disciplina; 


 # AVALIAÇOES REALIZADAS, NOTAS E RESPONSAVEIS
SELECT 
    m.id_matricula AS matricula, 
    av.nota, 
    p.nome AS professor, 
    a.nome AS aluno, 
    d.nome AS disciplina 
FROM tbl_avaliacoes av
JOIN tbl_professores p ON av.id_professor = p.id_professor
JOIN tbl_matriculas m ON av.id_matricula = m.id_matricula
JOIN tbl_alunos a ON m.id_aluno = a.id_aluno
JOIN tbl_disciplinas d ON m.id_disciplina = d.id_disciplina;

