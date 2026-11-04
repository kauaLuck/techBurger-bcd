# 🍔 Tech Burger — Banco de Dados Relacional (MySQL)

Este repositório contém a modelagem, a estrutura de tabelas (DDL) e os dados de teste (DML) para o sistema da hamburgueria **Tech Burger**. O banco de dados foi projetado em **MySQL** para gerenciar o fluxo completo de atendimento: desde o cadastro de clientes, categorias e produtos, até a emissão e acompanhamento de pedidos de entrega.

---

## 1. Estrutura do Banco de Dados

O banco `tech_burger` conta com 9 tabelas interligadas por chaves estrangeiras (`FOREIGN KEY`):

| Tabela | Descrição |
| :--- | :--- |
| `categorias` | Categorias dos produtos (Hambúrgueres, Bebidas, Acompanhamentos, Sobremesas). |
| `produtos` | Itens do cardápio com preço atual e vínculo com a categoria. |
| `clientes` | Informações de cadastro dos clientes (Nome, CPF e Telefone). |
| `enderecos` | Endereços de entrega associados aos clientes (`1:N`). |
| `entregadores` | Cadastro da equipe de entrega. |
| `forma_pagamentos` | Meios de pagamento aceitos (Cartão, PIX, Dinheiro). |
| `status_pedidos` | Etapas do pedido (Pendente, Em Rota, Entregue, Cancelado). |
| `pedidos` | Cabeçalho do pedido (Cliente, Endereço, Entregador, Status, Valor Total). |
| `item_pedidos` | Itens selecionados no pedido, quantidade e histórico do preço no momento da compra. |

---

## 2. Modelo Relacional

<img width="1014" height="818" alt="image" src="https://github.com/user-attachments/assets/4e3eddac-0c69-4a38-93ab-080b65e1959d" />


---

## 3. Script SQL Completo (Criação e Inserção)

Você pode executar o script abaixo diretamente no MySQL Workbench, DBeaver ou terminal MySQL para recriar todo o banco de dados.

```sql
-- 1. Criação e Seleção do Banco de Dados
CREATE DATABASE IF NOT EXISTS tech_burger;
USE tech_burger;

-- 2. Criação das Tabelas Principais (Sem Chaves Estrangeiras)
CREATE TABLE IF NOT EXISTS categorias (
    id_categoria INT AUTO_INCREMENT PRIMARY KEY,
    nome_categoria VARCHAR(100) NOT NULL
);

CREATE TABLE IF NOT EXISTS clientes (
    id_cliente INT AUTO_INCREMENT PRIMARY KEY,
    nome_cliente VARCHAR(100) NOT NULL,
    cpf_cliente VARCHAR(11) NOT NULL,
    telefone_cliente VARCHAR(15) NOT NULL
);

CREATE TABLE IF NOT EXISTS entregadores (
    id_entregador INT AUTO_INCREMENT PRIMARY KEY,
    nome_entregador VARCHAR(100) NOT NULL
);

CREATE TABLE IF NOT EXISTS forma_pagamentos (
    id_forma_pagamento INT AUTO_INCREMENT PRIMARY KEY,
    descricao VARCHAR(30) NOT NULL
);

CREATE TABLE IF NOT EXISTS status_pedidos (
    id_status INT AUTO_INCREMENT PRIMARY KEY,
    descricao VARCHAR(30) NOT NULL
);

-- 3. Criação das Tabelas Dependentes (Com Foreign Keys)
CREATE TABLE IF NOT EXISTS produtos (
    id_produto INT AUTO_INCREMENT PRIMARY KEY,
    id_categoria INT,
    nome_produto VARCHAR(100) NOT NULL,
    preco_atual DECIMAL(8,2) NOT NULL,
    FOREIGN KEY (id_categoria) REFERENCES categorias(id_categoria)
);

CREATE TABLE IF NOT EXISTS enderecos (
    id_endereco INT AUTO_INCREMENT PRIMARY KEY,
    id_cliente INT,
    logradouro VARCHAR(100) NOT NULL,
    numero VARCHAR(10) NOT NULL,
    complemento VARCHAR(50),
    bairro VARCHAR(50) NOT NULL,
    cep VARCHAR(8) NOT NULL,
    FOREIGN KEY (id_cliente) REFERENCES clientes(id_cliente) ON DELETE CASCADE
);

CREATE TABLE IF NOT EXISTS pedidos (
    id_pedido INT AUTO_INCREMENT PRIMARY KEY,
    id_cliente INT,
    id_endereco INT,
    id_entregador INT,
    id_forma_pagamento INT,
    id_status INT,
    data_hora_pedido DATETIME NOT NULL,
    observacoes TEXT NOT NULL,
    valor_total DECIMAL(8,2) NOT NULL,
    FOREIGN KEY (id_cliente) REFERENCES clientes(id_cliente) ON DELETE CASCADE,
    FOREIGN KEY (id_endereco) REFERENCES enderecos(id_endereco),
    FOREIGN KEY (id_entregador) REFERENCES entregadores(id_entregador),
    FOREIGN KEY (id_forma_pagamento) REFERENCES forma_pagamentos(id_forma_pagamento),
    FOREIGN KEY (id_status) REFERENCES status_pedidos(id_status)
);

CREATE TABLE IF NOT EXISTS item_pedidos (
    id_item_pedido INT AUTO_INCREMENT PRIMARY KEY,
    id_pedido INT,
    id_produto INT,
    quantidade INT NOT NULL,
    preco_historico DECIMAL(8,2) NOT NULL,
    FOREIGN KEY (id_pedido) REFERENCES pedidos(id_pedido),
    FOREIGN KEY (id_produto) REFERENCES produtos(id_produto)
);

-- 4. Inserção de Dados (Carga Inicial)

INSERT INTO categorias (id_categoria, nome_categoria) VALUES
(1, 'Hambúrgueres'),
(2, 'Acompanhamentos'),
(3, 'Bebidas'),
(4, 'Sobremesas');

INSERT INTO produtos (id_produto, id_categoria, nome_produto, preco_atual) VALUES
(1, 1, 'Tech Smash', 25.00),
(2, 1, 'Tech Bacon', 32.00),
(3, 1, 'Double Tech Cheese', 38.00),
(4, 1, 'Tech Veggie', 28.00),
(5, 1, 'Tech Monster', 45.00),
(6, 1, 'Tech Chicken', 27.00),
(7, 2, 'Batata Frita', 14.00),
(8, 3, 'Coca-Cola Zero', 6.00),
(9, 3, 'Guaraná', 6.00),
(10, 3, 'Suco de Laranja', 8.00),
(11, 3, 'Água Mineral', 4.00),
(12, 3, 'Coca-Cola', 6.00),
(13, 3, 'Cerveja IPA', 12.00),
(14, 4, 'Milkshake Chocolate', 18.00);

INSERT INTO forma_pagamentos (id_forma_pagamento, descricao) VALUES
(1, 'Cartão de Crédito'),
(2, 'Cartão de Débito'),
(3, 'PIX'),
(4, 'Dinheiro');

INSERT INTO status_pedidos (id_status, descricao) VALUES
(1, 'Pendente'),
(2, 'Em Rota'),
(3, 'Entregue'),
(4, 'Cancelado');

INSERT INTO entregadores (id_entregador, nome_entregador) VALUES
(1, 'Roberto Silva'),
(2, 'Carlos Moto');

INSERT INTO clientes (id_cliente, nome_cliente, cpf_cliente, telefone_cliente) VALUES
(1, 'João da Silva', '11122233344', '11988881111'),
(2, 'Maria Oliveira', '22233344455', '11977772222'),
(3, 'Ana Souza', '33344455566', '11966663333'),
(4, 'Lucas Mendes', '00000000000', '11955554444'),
(5, 'Fernanda Lima', '44455566677', '11944445555'),
(6, 'Paulo Santos', '55566677788', '11933332222');

INSERT INTO enderecos (id_endereco, id_cliente, logradouro, numero, complemento, bairro, cep) VALUES
(1, 1, 'Rua das Flores', '123', 'casa', 'Centro', '01000000'),
(2, 2, 'Avenida Paulista', '1000', 'Apto 42', 'Centro', '01310100'),
(3, 3, 'Rua Bela Vista', '45', 'casa', 'Jardim das Rosas', '01400000'),
(4, 4, 'Rua Central', '88', 'bloco B', 'Centro', '01001000'),
(5, 5, 'Rua Pedroso', '90', 'casa', 'Bela Vista', '01320000'),
(6, 6, 'Alameda Santos', '500', 'bloco A', 'Cerqueira César', '01418000');

INSERT INTO pedidos (id_pedido, id_cliente, id_endereco, id_entregador, id_forma_pagamento, id_status, data_hora_pedido, observacoes, valor_total) VALUES
(1, 1, 1, 1, 1, 3, '2026-08-01 19:10:00', 'Sem cebola', 62.00),
(2, 2, 2, 1, 3, 3, '2026-08-01 19:25:00', 'Bem passado', 38.00),
(3, 1, 1, 2, 4, 3, '2026-08-01 19:40:00', 'Maio extra', 14.00),
(4, 3, 3, 1, 3, 3, '2026-08-01 20:00:00', 'Capricha no molho', 46.00),
(5, 4, 4, NULL, 1, 4, '2026-08-01 20:15:00', 'no ponto', 64.00),
(6, 2, 2, 2, 2, 3, '2026-08-01 20:30:00', 'Molho à parte', 93.00),
(7, 5, 5, 1, 3, 2, '2026-08-01 20:45:00', 'Extra queijo', 59.00),
(8, 1, 1, 2, 4, 3, '2026-08-01 21:00:00', 'mal passado', 27.00),
(9, 6, 6, 1, 1, 3, '2026-08-01 21:20:00', 'Gelada', 49.00),
(10, 3, 3, 2, 3, 3, '2026-08-01 21:35:00', 'Copo bolha', 36.00);

INSERT INTO item_pedidos (id_pedido, id_produto, quantidade, preco_historico) VALUES
(1, 1, 2, 25.00),
(1, 8, 2, 6.00),
(2, 2, 1, 32.00),
(2, 9, 1, 6.00),
(3, 7, 1, 14.00),
(4, 3, 1, 38.00),
(4, 10, 1, 8.00),
(5, 4, 2, 28.00),
(5, 11, 2, 4.00),
(6, 1, 3, 25.00),
(6, 12, 3, 6.00),
(7, 5, 1, 45.00),
(7, 7, 1, 14.00),
(8, 6, 1, 27.00),
(9, 1, 1, 25.00),
(9, 13, 2, 12.00),
(10, 14, 2, 18.00);
```

---

## 4. Consultas Úteis (Queries de Exemplo)

### Listar todos os pedidos com detalhes de cliente, entregador e status
```sql
SELECT 
    p.id_pedido,
    c.nome_cliente,
    e.nome_entregador,
    s.descricao AS status_pedido,
    fp.descricao AS forma_pagamento,
    p.valor_total,
    p.data_hora_pedido
FROM pedidos p
INNER JOIN clientes c ON p.id_cliente = c.id_cliente
LEFT JOIN entregadores e ON p.id_entregador = e.id_entregador
INNER JOIN status_pedidos s ON p.id_status = s.id_status
INNER JOIN forma_pagamentos fp ON p.id_forma_pagamento = fp.id_forma_pagamento;
```

### Produtos mais vendidos e faturamento por produto
```sql
SELECT 
    pr.nome_produto,
    SUM(ip.quantidade) AS total_unidades_vendidas,
    SUM(ip.quantidade * ip.preco_historico) AS faturamento_total
FROM item_pedidos ip
INNER JOIN produtos pr ON ip.id_produto = pr.id_produto
GROUP BY pr.id_produto, pr.nome_produto
ORDER BY total_unidades_vendidas DESC;
```

---

## 5. Como Importar e Exportar o Arquivo `.sql`

### Exportar o banco para `.sql` (Dump)

* **Via Terminal:**
  ```bash
  mysqldump -u root -p tech_burger > tech_burger.sql
  ```
* **Via MySQL Workbench:**
  1. Vá ao menu **Server** → **Data Export**.
  2. Selecione o banco `tech_burger`.
  3. Escolha **Export to Self-Contained File**.
  4. Clique em **Start Export**.

### Importar o arquivo `.sql`

* **Via Terminal:**
  ```bash
  mysql -u root -p tech_burger < tech_burger.sql
  ```
