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

## 2. Comando básico de criação de banco

```sql
CREATE DATABASE netflix_aula
  DEFAULT CHARACTER SET utf8mb4
  COLLATE utf8mb4_0900_ai_ci;
```

### 🔎 Explicando cada parte:
- **CREATE DATABASE** → cria um novo banco de dados.  
- **DEFAULT CHARACTER SET utf8mb4** → define o **conjunto de caracteres**.  
  - `utf8mb4` é a versão mais completa do UTF-8 e aceita **emojis e caracteres especiais**.  
- **COLLATE utf8mb4_0900_ai_ci** → define as **regras de comparação** entre caracteres.  
  - *Exemplo:* se `COLLATE` for _ci_ (case-insensitive), `Maria` = `maria`.  
  - *Se fosse cs (case-sensitive), `Maria` ≠ `maria`.*  

📌 **Resumo:** Charset = quais letras/símbolos posso armazenar.  
Collate = como o banco compara e ordena essas letras/símbolos.

---

## 3. Criando Tabelas

### Exemplo:

```sql
CREATE TABLE usuario (
  id_usuario INT AUTO_INCREMENT,
  nome       VARCHAR(100) NOT NULL,
  email      VARCHAR(100) NOT NULL UNIQUE,
  senha      VARCHAR(255) NOT NULL,
  PRIMARY KEY (id_usuario)
) ENGINE=InnoDB;
```

### 🔎 Explicando cada parte:
- **id_usuario INT AUTO_INCREMENT**  
  - `INT` → número inteiro.  
  - `AUTO_INCREMENT` → gera números automáticos (1, 2, 3…).  
  - Usado para **identificar cada registro** sem repetição.  

- **VARCHAR(100)**  
  - Texto de até 100 caracteres.  
  - Exemplo: nome, email.  

- **NOT NULL**  
  - Obriga o campo a ter valor.  
  - Exemplo: não posso cadastrar um usuário sem nome.  

- **UNIQUE**  
  - Não permite valores repetidos.  
  - Exemplo: dois usuários não podem ter o mesmo email.  

- **PRIMARY KEY**  
  - Identificador único da tabela.  
  - Exemplo: `id_usuario` diferencia cada pessoa.  

- **ENGINE=InnoDB**  
  - Define como a tabela vai funcionar dentro do MySQL.  
  - `InnoDB` é o motor mais usado porque suporta **transações**, **chaves estrangeiras (FK)** e **integridade dos dados**.  
  - Outros engines existem (como MyISAM), mas são mais limitados.  

📌 **Resumo:** InnoDB = garante segurança e relacionamentos no banco.

---

## 4. O que é CONSTRAINT?
**Constraint** = **restrição** que define regras de integridade no banco.  
Elas **garantem que os dados estejam corretos**.  

Tipos de constraints:
- **PRIMARY KEY** → chave primária (identidade única).  
- **FOREIGN KEY** → chave estrangeira (ligação entre tabelas).  
- **UNIQUE** → não permite duplicados.  
- **NOT NULL** → não pode ser vazio.  
- **DEFAULT** → valor padrão quando nada for informado.  
- **CHECK** → valida uma regra lógica (ex: nota entre 1 e 5).  

Exemplo:

```sql
CONSTRAINT fk_perfil_usuario
  FOREIGN KEY (id_usuario) REFERENCES usuario(id_usuario)
  ON DELETE CASCADE
  ON UPDATE CASCADE
```

---

## 5. Chaves Estrangeiras (FOREIGN KEY)
A **Foreign Key (FK)** cria uma ligação entre duas tabelas.  
👉 É como dizer: *“esse perfil pertence a um usuário”*.

### Exemplo:
```sql
CREATE TABLE perfil (
  id_perfil INT AUTO_INCREMENT PRIMARY KEY,
  id_usuario INT NOT NULL,
  nome_perfil VARCHAR(50) NOT NULL,
  CONSTRAINT fk_perfil_usuario
    FOREIGN KEY (id_usuario) REFERENCES usuario(id_usuario)
    ON DELETE CASCADE
    ON UPDATE CASCADE
);
```

---

## 6. ON DELETE / ON UPDATE
Essas opções definem **o que acontece quando a linha relacionada muda ou é apagada**.

### 🔎 Opções:
- **CASCADE** → a ação se repete na tabela filha.  
  - Se apagar o usuário, apaga todos os perfis dele.  
- **SET NULL** → a FK vira NULL.  
  - Se apagar o gênero, o filme fica sem gênero (NULL).  
- **RESTRICT / NO ACTION** → impede a ação se houver dependentes.  
  - Se tentar apagar um usuário que tem perfil, o MySQL não deixa.  

📌 **Resumo:**  
- CASCADE = filho morre junto com o pai.  
- SET NULL = filho fica órfão (mas continua existindo).  
- RESTRICT = o pai não pode ser apagado se tem filhos.  

---

## 7. ALTER TABLE
Serve para **modificar tabelas já existentes**.

Exemplo:

```sql
-- Adicionar uma coluna
ALTER TABLE usuario
  ADD COLUMN ativo BOOLEAN DEFAULT TRUE;

-- Alterar tipo de uma coluna
ALTER TABLE filme
  MODIFY titulo VARCHAR(180) NOT NULL;

-- Criar índice (ajuda em consultas rápidas)
CREATE INDEX ix_filme_titulo ON filme(titulo);
```

---

## 8. DROP
Serve para **apagar** banco, tabela ou objeto.

Exemplo:
```sql
DROP TABLE avaliacao;
DROP DATABASE netflix_aula;
```

⚠️ **Cuidado!** DROP apaga tudo definitivamente.

---

## 9. CHECK
Garante que os dados sigam uma regra.  
⚠️ Funciona apenas no MySQL 8.0.16 ou superior.

Exemplo:
```sql
CREATE TABLE avaliacao (
  id_perfil INT,
  id_filme INT,
  nota TINYINT,
  CHECK (nota BETWEEN 1 AND 5)
);
```

📌 Assim, nunca será possível salvar nota 0 ou 6.

---

## 10. Resumão dos conceitos

| Conceito       | Significado                                                                 | Exemplo |
|----------------|-----------------------------------------------------------------------------|---------|
| ENGINE=InnoDB  | Motor da tabela, suporta transações e FK                                    | `CREATE TABLE ... ENGINE=InnoDB;` |
| CONSTRAINT     | Restrição para garantir integridade                                          | `PRIMARY KEY`, `FOREIGN KEY`, `UNIQUE` |
| COLLATE        | Como o banco compara/ordena textos                                          | `utf8mb4_0900_ai_ci` |
| CASCADE        | Propaga ação (delete/update) da tabela pai para a filha                     | `ON DELETE CASCADE` |
| SET NULL       | Deixa o campo da FK nulo quando o pai é apagado                             | `ON DELETE SET NULL` |
| RESTRICT       | Impede apagar/alterar se existir dependentes                                | `ON DELETE RESTRICT` |
| DEFAULT        | Define valor automático caso não seja informado                             | `idioma DEFAULT 'pt-BR'` |
| AUTO_INCREMENT | Gera valores automáticos para PK                                            | `id_usuario INT AUTO_INCREMENT` |

---

## 🎯 Encerrando
- DDL cria a **estrutura** do banco.  
- Constraints mantêm os dados **corretos e confiáveis**.  
- ENGINE, Charset e Collation garantem **compatibilidade e segurança**.  
- ON DELETE/UPDATE define **o comportamento dos relacionamentos**.  

👉 Agora você já consegue **pegar um DER e transformá-lo em tabelas reais no MySQL**.
