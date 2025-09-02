# 🏗️ Aula-2 – DDL (Data Definition Language) no MySQL

## 1. O que é DDL?
DDL significa **Data Definition Language** (Linguagem de Definição de Dados).  
👉 É o conjunto de comandos SQL usados para **criar, alterar e excluir a estrutura** do banco de dados.

- **CREATE** → criar tabelas, bancos, índices, etc.  
- **ALTER** → alterar tabelas (adicionar/remover colunas, chaves, índices).  
- **DROP** → excluir tabelas, bancos ou objetos.  
- **TRUNCATE** → limpar todos os dados de uma tabela, mas manter a estrutura.

📌 **Resumo:** DDL define **como o banco será construído**.  
*(Já o DML manipula os dados, e o DCL/TCL controla permissões e transações.)*

---

## 2. Criando o Banco de Dados

```sql
CREATE DATABASE IF NOT EXISTS `netflix_db`
DEFAULT CHARACTER SET utf8mb4       -- Conjunto de caracteres (aceita emojis e acentos)
COLLATE utf8mb4_0900_ai_ci;         -- Regras de comparação (case-insensitive)
````

* **IF NOT EXISTS** → evita erro se o banco já existir.
* **CHARACTER SET** → define quais caracteres podem ser armazenados.
* **COLLATE** → define como os textos serão comparados e ordenados.

```sql
USE `netflix_db`;  -- Seleciona o banco para usar
```

---

## 3. Criando Tabelas com Comentários Didáticos

### 3.1 Tabela `usuario`

```sql
CREATE TABLE usuario (
    id_usuario INT UNSIGNED AUTO_INCREMENT PRIMARY KEY, -- PK: identifica cada usuário
    nome VARCHAR(100) NOT NULL,                         -- nome do usuário (obrigatório)
    email VARCHAR(100) UNIQUE NOT NULL,                 -- email único (não pode repetir)
    senha VARCHAR(255) NOT NULL                         -- senha criptografada (obrigatório)
) ENGINE=InnoDB;                                       -- Motor que suporta FK e transações
```

---

### 3.2 Tabela `perfil`

```sql
CREATE TABLE perfil (
    id_perfil INT UNSIGNED AUTO_INCREMENT PRIMARY KEY, -- PK: identifica cada perfil
    id_usuario INT UNSIGNED NOT NULL,                  -- FK: vincula perfil ao usuário
    nome_perfil VARCHAR(50) NOT NULL,                  -- nome do perfil
    idioma VARCHAR(20) DEFAULT 'pt-BR',                -- idioma padrão do perfil
    controle_parental BOOLEAN DEFAULT FALSE,           -- controle parental (true/false)
    CONSTRAINT fk_perfil_usuario
        FOREIGN KEY (id_usuario) REFERENCES usuario(id_usuario)
        ON DELETE CASCADE                               -- se usuário for apagado, perfis também
        ON UPDATE CASCADE                               -- se id_usuario mudar, atualiza aqui
) ENGINE=InnoDB;
```

---

### 3.3 Tabela `genero`

```sql
CREATE TABLE genero (
    id_genero INT UNSIGNED AUTO_INCREMENT PRIMARY KEY, -- PK: identifica cada gênero
    nome VARCHAR(50) NOT NULL                           -- nome do gênero
) ENGINE=InnoDB;
```

---

### 3.4 Tabela `filme`

```sql
CREATE TABLE filme (
    id_filme INT UNSIGNED AUTO_INCREMENT PRIMARY KEY, -- PK: identifica cada filme
    titulo VARCHAR(150) NOT NULL,                     -- título do filme
    ano INT,                                          -- ano de lançamento
    duracao INT,                                      -- duração em minutos
    id_genero INT UNSIGNED,                           -- FK: gênero do filme
    FOREIGN KEY (id_genero) REFERENCES genero(id_genero)
        ON DELETE SET NULL                            -- se gênero apagado, filme fica sem gênero
        ON UPDATE CASCADE                             -- se id_genero mudar, atualiza aqui
) ENGINE=InnoDB;
```

---

### 3.5 Tabela `avaliacao`

```sql
CREATE TABLE avaliacao (
    id_perfil INT UNSIGNED NOT NULL,   -- FK: perfil que avaliou
    id_filme INT UNSIGNED NOT NULL,    -- FK: filme avaliado
    nota TINYINT UNSIGNED NOT NULL,    -- nota de 1 a 5
    comentario TEXT,                   -- comentário opcional
    PRIMARY KEY (id_perfil, id_filme), -- PK composta (N:N)
    FOREIGN KEY (id_perfil) REFERENCES perfil(id_perfil)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    FOREIGN KEY (id_filme) REFERENCES filme(id_filme)
        ON DELETE CASCADE
        ON UPDATE CASCADE,
    CHECK (nota BETWEEN 1 AND 5)       -- valida que nota está entre 1 e 5
) ENGINE=InnoDB;
```

---

## 4. Alterando Tabelas (ALTER TABLE)

```sql
-- Adicionar coluna
ALTER TABLE usuario
  ADD COLUMN ativo BOOLEAN DEFAULT TRUE; -- campo para soft delete

-- Alterar tipo de coluna
ALTER TABLE filme
  MODIFY titulo VARCHAR(180) NOT NULL;

-- Criar índice para consultas rápidas
CREATE INDEX ix_filme_titulo ON filme(titulo);
```

---

## 5. Apagando Objetos (DROP)

```sql
DROP TABLE avaliacao;       -- apaga a tabela avaliacao
DROP DATABASE netflix_db;   -- apaga o banco inteiro
```

⚠️ **Atenção:** DROP apaga tudo permanentemente!

---

## 6. Resumão dos Conceitos

| Conceito        | Significado                                 | Exemplo                                |
| --------------- | ------------------------------------------- | -------------------------------------- |
| ENGINE=InnoDB   | Motor da tabela, suporta FK e transações    | `ENGINE=InnoDB`                        |
| CONSTRAINT      | Restrição para manter integridade           | `PRIMARY KEY`, `FOREIGN KEY`, `UNIQUE` |
| COLLATE         | Define regras de comparação de textos       | `utf8mb4_0900_ai_ci`                   |
| CASCADE         | Propaga ação de pai para filho              | `ON DELETE CASCADE`                    |
| SET NULL        | Deixa FK nula se pai for apagado            | `ON DELETE SET NULL`                   |
| RESTRICT        | Impede apagar/alterar se houver dependentes | `ON DELETE RESTRICT`                   |
| DEFAULT         | Valor padrão para coluna                    | `idioma DEFAULT 'pt-BR'`               |
| AUTO\_INCREMENT | Gera valores automáticos para PK            | `id_usuario INT AUTO_INCREMENT`        |

---

## 7. Encerrando

* DDL cria a **estrutura do banco**.
* Constraints garantem que os dados fiquem **corretos e confiáveis**.
* ENGINE, Charset e Collation garantem **compatibilidade e segurança**.
* ON DELETE/UPDATE define o **comportamento dos relacionamentos**.

> Agora você já consegue **pegar um DER e transformar em tabelas reais no MySQL**, com todas as regras de integridade aplicadas.

