# 🧑‍💻 SQL Examples Portfolio

Coleção de consultas SQL para prática, demonstração de habilidades e análise de dados em diferentes cenários.

---

## 📖 Sobre o projeto

Este repositório reúne consultas SQL desenvolvidas para fins de aprendizado, testes e demonstração do domínio da linguagem SQL.  
São queries com diferentes níveis de complexidade, aplicando funções de agregação, filtros, joins, condicionais e cálculos, com foco em análise de dados de vendas, leads e comportamento de usuários.

---

## 🎯 Objetivos

- Demonstrar domínio dos principais comandos e técnicas SQL.  
- Praticar construção de queries variadas e aplicadas a cenários reais.  
- Explorar funções agregadas, cláusulas condicionais e junções.  
- Servir como referência pessoal e material didático para quem quer aprender SQL.

---

## 📚 Conteúdo

- Análise de dados demográficos (gênero, faixa etária, status profissional).  
- Análise de comportamento do funil de vendas (visitas, conversão, receita).  
- Agrupamentos, filtros, cálculos de métricas e uso de CTEs.  
- Junções entre tabelas para enriquecer os dados.

---

## 📝 Exemplos de Queries

### 1. Gênero dos leads

```sql
SELECT
  CASE
    WHEN ibge.gender = 'male' THEN 'homens'
    WHEN ibge.gender = 'female' THEN 'mulheres'
  END AS "gênero",
  COUNT(*) AS "leads (#)"
FROM sales.customers AS cus
LEFT JOIN temp_tables.ibge_genders AS ibge
  ON lower(cus.first_name) = lower(ibge.first_name)
GROUP BY ibge.gender;
2. Status profissional dos leads
sql
Copiar
Editar
SELECT
  CASE
    WHEN professional_status = 'freelancer' THEN 'freelancer'
    WHEN professional_status = 'retired' THEN 'aposentado(a)'
    WHEN professional_status = 'clt' THEN 'clt'
    WHEN professional_status = 'self_employed' THEN 'autônomo(a)'    
    WHEN professional_status = 'other' THEN 'outro'
    WHEN professional_status = 'businessman' THEN 'empresário(a)'
    WHEN professional_status = 'civil_servant' THEN 'funcionário(a) público(a)'
    WHEN professional_status = 'student' THEN 'estudante'
  END AS "status profissional",
  (COUNT(*)::float) / (SELECT COUNT(*) FROM sales.customers) AS "leads (%)"
FROM sales.customers
GROUP BY professional_status;
3. Veículos mais visitados por marca
sql
Copiar
Editar
SELECT
  pro.brand,
  pro.model,
  COUNT(*) AS "visitas (#)"
FROM sales.funnel AS fun
LEFT JOIN sales.products AS pro
  ON fun.product_id = pro.product_id
GROUP BY pro.brand, pro.model
ORDER BY pro.brand, pro.model, "visitas (#)";
4. Receita, leads, conversão e ticket médio mês a mês
sql
Copiar
Editar
WITH leads AS (
  SELECT
    date_trunc('month', visit_page_date)::date AS visit_page_month,
    COUNT(*) AS visit_page_count
  FROM sales.funnel
  GROUP BY visit_page_month
  ORDER BY visit_page_month
),
payments AS (
  SELECT
    date_trunc('month', fun.paid_date)::date AS paid_month,
    COUNT(fun.paid_date) AS paid_count,
    SUM(pro.price * (1 + fun.discount)) AS receita
  FROM sales.funnel AS fun
  LEFT JOIN sales.products AS pro
    ON fun.product_id = pro.product_id
  WHERE fun.paid_date IS NOT NULL
  GROUP BY paid_month
  ORDER BY paid_month
)
SELECT
  leads.visit_page_month AS "mês",
  leads.visit_page_count AS "leads (#)",
  payments.paid_count AS "vendas (#)",
  (payments.receita / 1000) AS "receita (k, R$)",
  (payments.paid_count::float / leads.visit_page_count::float) AS "conversão (%)",
  (payments.receita / payments.paid_count / 1000) AS "ticket médio (k, R$)"
FROM leads
LEFT JOIN payments
  ON leads.visit_page_month = payments.paid_month;
⚙️ Como usar
bash
Copiar
Editar
git clone https://github.com/LucasDiagone/SQL-Examples-Portfolio.git
Explore os arquivos .sql na pasta queries/ para ver as consultas completas.

Importe e execute as queries no seu banco de dados (PostgreSQL ou outro compatível).

Adapte as consultas conforme seus estudos ou projetos.

📂 Estrutura do repositório
pgsql
Copiar
Editar
/queries
  genero_leads.sql
  status_profissional_leads.sql
  veiculos_mais_visitados.sql
  receita_leads_conversao.sql
README.md

📬 Contato
Sinta-se à vontade para abrir issues ou entrar em contato:

**Lucas Diagone**

(https://www.linkedin.com/in/lucas-diagone-691285104/) | (https://github.com/LucasDiagone)

