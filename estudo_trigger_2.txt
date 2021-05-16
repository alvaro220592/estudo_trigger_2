-- DROP DATABASE estudo_trigger2;
CREATE DATABASE estudo_trigger2;
USE estudo_trigger2;


# ============== Criação de tabelas ============= #
CREATE TABLE produtos
(
	id INT PRIMARY KEY AUTO_INCREMENT, -- chave primária com auto incremento
	nome VARCHAR(50) UNIQUE, -- Não se repetem produtos com mesmo nome
	estoque INT NOT NULL DEFAULT 0 -- Caso num sistema, esse dado não seja inserido, já vem com zero
);

CREATE TABLE vendas
(
	id INT PRIMARY KEY AUTO_INCREMENT,
	produto_id INT NOT NULL,
	quantidade INT,
    data_venda TIMESTAMP, -- captura data e hora atuais ao realizar a inserção
    FOREIGN KEY (produto_id) REFERENCES produtos (id) 	-- chave estrangeira 'produto_id' referente ao 'id' da tabela 'produtos'
);


# ============ Criação dos triggers ============ #
DELIMITER $
CREATE TRIGGER tr_vendas_insert AFTER INSERT
ON vendas
FOR EACH ROW
BEGIN
UPDATE produtos SET estoque = estoque - NEW.quantidade
WHERE id = NEW.produto_id;
END$

CREATE TRIGGER tr_vendas_delete AFTER DELETE
ON vendas
FOR EACH ROW
BEGIN
UPDATE produtos SET estoque = estoque + OLD.quantidade
WHERE id = OLD.produto_id;
END$
DELIMITER ;


# ============ Inserções nas tabelas ============ #
INSERT INTO produtos (nome, estoque) VALUES
	('Feijão', 10),
    ('Arroz', 10),
    ('Farinha', 10);
    
INSERT INTO vendas(produto_id, quantidade) VALUES
	(1, 2),
    (2, 4),
    (3, 9);

# ======= Exclusões de dados nas tabelas ========= #
DELETE FROM vendas WHERE produto_id = 3;

# ================= Consultas ================== #    

SELECT * FROM produtos;

SELECT * FROM vendas;

SELECT P.id, P.nome, V.quantidade, DATE_FORMAT(V.data_venda, '%d-%m-%Y às %h:%m:%s') as 'data_venda' 
FROM produtos as P INNER JOIN vendas AS V
ON P.id = V.produto_id;