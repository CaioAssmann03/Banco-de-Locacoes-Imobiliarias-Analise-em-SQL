# 🏠 Banco de Locações Imobiliárias — Análise em SQL

Modelagem e análise de um banco de dados de locações imobiliárias, inspirado na minha
vivência profissional como Assistente Administrativo na Eve Imóveis. Dados fictícios,
estrutura e desafios de negócio realistas.

---

## 📐 Modelagem

4 tabelas relacionadas — imóveis, proprietários, locatários e contratos:

```sql
CREATE TABLE imoveis (
    id INTEGER PRIMARY KEY,
    endereco TEXT,
    bairro TEXT,
    tipo TEXT,           -- apartamento, casa, comercial, cobertura, loja
    valor_aluguel REAL,
    status TEXT          -- disponivel, alugado, manutencao
);

CREATE TABLE proprietarios (
    id INTEGER PRIMARY KEY,
    nome TEXT,
    telefone TEXT,
    imovel_id INTEGER REFERENCES imoveis(id)
);

CREATE TABLE locatarios (
    id INTEGER PRIMARY KEY,
    nome TEXT,
    telefone TEXT
);

CREATE TABLE contratos (
    id INTEGER PRIMARY KEY,
    imovel_id INTEGER REFERENCES imoveis(id),
    locatario_id INTEGER REFERENCES locatarios(id),
    data_inicio DATE,
    data_fim DATE,
    valor_mensal REAL,
    status_pagamento TEXT  -- em_dia, atrasado, quitado
);
```

**Volume de dados:** 150 imóveis · 150 proprietários · 120 locatários · 80 contratos

---

## 📊 Consultas — Ocupação e Receita

### 1. Taxa de ocupação
```sql
SELECT
    COUNT(*) AS total_imoveis,
    SUM(CASE WHEN status = 'alugado' THEN 1 ELSE 0 END) AS alugados,
    ROUND(SUM(CASE WHEN status = 'alugado' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS taxa_ocupacao
FROM imoveis;
```
**Resultado:** _(preencher)_

### 2. Receita mensal de aluguéis
```sql
SELECT SUM(valor_mensal) AS receita_mensal
FROM contratos
WHERE status_pagamento IN ('em_dia','atrasado');
```
**Resultado:** _(preencher)_

### 3. Receita mensal por status de pagamento
```sql
SELECT status_pagamento, SUM(valor_mensal) AS receita
FROM contratos
WHERE status_pagamento IN ('em_dia','atrasado')
GROUP BY status_pagamento;
```
**Resultado:** _(preencher)_

### 4. Percentual de inadimplência
```sql
SELECT
    COUNT(*) AS contratos_ativos,
    SUM(CASE WHEN status_pagamento = 'atrasado' THEN 1 ELSE 0 END) AS atrasados,
    ROUND(SUM(CASE WHEN status_pagamento = 'atrasado' THEN 1 ELSE 0 END) * 100.0 / COUNT(*), 2) AS percentual_inadimplencia
FROM contratos
WHERE status_pagamento IN ('em_dia','atrasado');
```
**Resultado:** _(preencher)_

### 5. Ticket médio dos contratos ativos
```sql
SELECT ROUND(AVG(valor_mensal), 2) AS ticket_medio
FROM contratos
WHERE status_pagamento IN ('em_dia','atrasado');
```
**Resultado:** _(preencher)_

### 6. Evolução dos contratos ao longo dos anos
```sql
SELECT strftime('%Y', data_inicio) AS ano, COUNT(*) AS contratos
FROM contratos
GROUP BY ano
ORDER BY ano;
```
**Resultado:** _(preencher)_

### 7. Tempo médio de permanência dos locatários
```sql
SELECT ROUND(AVG((julianday(data_fim) - julianday(data_inicio)) / 30), 1) AS meses
FROM contratos;
```
**Resultado:** _(preencher)_

---

## 🏘️ Consultas — Bairros e Tipos de Imóvel

### 8. Aluguel médio por bairro
```sql
SELECT bairro, ROUND(AVG(valor_aluguel), 2) AS aluguel_medio
FROM imoveis
GROUP BY bairro
ORDER BY aluguel_medio DESC;
```
**Resultado:** _(preencher)_

### 9. Aluguel médio por tipo de imóvel
```sql
SELECT tipo, ROUND(AVG(valor_aluguel), 2) AS aluguel_medio
FROM imoveis
GROUP BY tipo
ORDER BY aluguel_medio DESC;
```
**Resultado:** _(preencher)_

### 10. Ranking dos bairros mais valorizados (com quantidade de imóveis)
```sql
SELECT bairro, COUNT(*) AS quantidade, ROUND(AVG(valor_aluguel), 2) AS media_aluguel
FROM imoveis
GROUP BY bairro
ORDER BY media_aluguel DESC;
```
**Resultado:** _(preencher)_

### 11. Quantidade de imóveis disponíveis por região
```sql
SELECT bairro, COUNT(*) AS disponiveis
FROM imoveis
WHERE status = 'disponivel'
GROUP BY bairro
ORDER BY disponiveis DESC;
```
**Resultado:** _(preencher)_

### 12. Bairros com maior número de imóveis
```sql
SELECT bairro, COUNT(*) AS quantidade
FROM imoveis
GROUP BY bairro
ORDER BY quantidade DESC;
```
**Resultado:** _(preencher)_

### 13. Quantidade de imóveis por tipo
```sql
SELECT tipo, COUNT(*) AS quantidade
FROM imoveis
GROUP BY tipo;
```
**Resultado:** _(preencher)_

### 14. Quantidade de imóveis por status
```sql
SELECT status, COUNT(*) AS quantidade
FROM imoveis
GROUP BY status;
```
**Resultado:** _(preencher)_

---

## 👥 Consultas — Proprietários e Locatários

### 15. Receita por proprietário
```sql
SELECT p.nome, SUM(c.valor_mensal) AS receita
FROM proprietarios p
JOIN contratos c ON p.imovel_id = c.imovel_id
WHERE c.status_pagamento IN ('em_dia','atrasado')
GROUP BY p.nome
ORDER BY receita DESC;
```
**Resultado:** _(preencher)_

### 16. Top 10 locatários com maior gasto total
```sql
SELECT
    l.id,
    l.nome,
    COUNT(c.id) AS quantidade_contratos,
    SUM(c.valor_mensal) AS valor_total_pago
FROM locatarios l
JOIN contratos c ON l.id = c.locatario_id
GROUP BY l.id, l.nome
ORDER BY valor_total_pago DESC
LIMIT 10;
```
**Resultado:** _(preencher)_

---

## 🏆 Consultas — Rankings

### 17. Top 10 imóveis mais caros
```sql
SELECT endereco, bairro, tipo, valor_aluguel
FROM imoveis
ORDER BY valor_aluguel DESC
LIMIT 10;
```
**Resultado:** _(preencher)_

### 18. Top 10 maiores aluguéis (contratos ativos)
```sql
SELECT i.endereco, i.bairro, c.valor_mensal
FROM contratos c
JOIN imoveis i ON i.id = c.imovel_id
ORDER BY c.valor_mensal DESC
LIMIT 10;
```
**Resultado:** _(preencher)_

### 19. Valor total da carteira imobiliária
```sql
SELECT SUM(valor_aluguel) AS carteira
FROM imoveis;
```
**Resultado:** _(preencher)_

---

## 🔎 Bônus: auditoria de qualidade de dados

Cruzando as tabelas `imoveis` e `contratos`, encontrei uma inconsistência: existem imóveis
marcados com status **"alugado"** que não têm nenhum contrato correspondente no banco.

```sql
SELECT id, endereco, bairro, status
FROM imoveis
WHERE status = 'alugado'
AND id NOT IN (SELECT imovel_id FROM contratos);
```
**Resultado:** _(preencher)_

Isso simula um problema comum em bases reais: um campo de status desatualizado manualmente
que não reflete a fonte de verdade (a tabela de contratos). Numa situação real, esse seria o
tipo de achado que eu reportaria antes de confiar em qualquer dashboard construído em cima
desses dados.

---

## 🛠️ Ferramentas
SQLite · SQL (JOIN, GROUP BY, CASE WHEN, subqueries, funções de data)

## ▶️ Como rodar
1. Baixe `Imobiliaria.db` e `consultas.sql` deste repositório
2. Abra o `.db` no [DB Browser for SQLite](https://sqlitebrowser.org/) (gratuito) ou em [sqliteonline.com](https://sqliteonline.com)
3. Execute as queries de `consultas.sql` na aba "Execute SQL"

## 💡 Aprendizados
- _(preencher — 1 a 2 linhas do que foi mais desafiador ou do que você entendeu melhor)_

---

## 👤 Sobre mim
Caio Assmann — estudante de Análise e Desenvolvimento de Sistemas, focado em Dados e Business Intelligence.

📊 [Portfólio](#) · 💼 [LinkedIn](https://www.linkedin.com/in/caio-assmann/) · 📩 caioassmann7@gmail.com
