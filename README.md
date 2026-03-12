**2.3. Exercícios SQL Aplicados (Dados de Mobilidade)**



**A. Liste as linhas de ônibus, com o nome e número da linha**
'''sql
select
    tx_linha,
    cd_linha
from 
	dados_mobilidade.tab_linha
'''
--------------------------------------------------------------------------------------------------------

**B. Apresente apenas o nome, código da linha e extensão da 0.111**

select
	tl.tx_linha,
	tl.id_linha, 
	ti.lin_extensao
from 
	dados_mobilidade.tab_linha tl 
join
    dados_mobilidade.tab_itinerario ti
 on ti.id_linha = tl.id_linha
where tl.cd_linha = '0.111'

--------------------------------------------------------------------------------------------------------

**C. Quais linhas tem a extensão maior que 70km ?**

select 
	cd_linha, tx_linha
from 
    tab_linha li
join
    tab_itinerario ti 
  on ti.id_linha = li.id_linha
where 
	lin_extensao > 70
order by cd_linha, tx_linha DESC

--------------------------------------------------------------------------------------------------------

**D. Qual é a linha mais curta ?**

SELECT MIN (LIN_EXTENSAO)
FROM tab_itinerario ti

--------------------------------------------------------------------------------------------------------

**E. Qual é a linha mais longa ?**

SELECT MAX (LIN_EXTENSAO)
FROM tab_itinerario ti

--------------------------------------------------------------------------------------------------------

**F. Qual a média das extensões das linhas ?**

select AVG(lin_extensao)
from dados_mobilidade.tab_itinerario

--------------------------------------------------------------------------------------------------------

**G. Quantas são as linhas circulares ?**

SELECT 
    COUNT(CASE WHEN lin_sentido ILIKE 'CIRCULAR' THEN 1 END) AS Total_Circular,
    COUNT(CASE WHEN lin_sentido ILIKE 'IDA' THEN 1 END) AS Total_Ida,
    COUNT(CASE WHEN lin_sentido ILIKE 'VOLTA' THEN 1 END) AS Total_Volta
FROM dados_mobilidade.tab_itinerario ti ;
                              --Ou
SELECT 
    COUNT(CASE WHEN tx_linha ILIKE '%Circular%' THEN 1 END) AS Total_Circular
FROM tab_linha;

--------------------------------------------------------------------------------------------------------

**H. Qual a linha de sentido circular que é mais longa ?**

select 
	lin_extensao,lin_sentido
from 
    tab_itinerario ti
where ti.lin_sentido ilike 'CIRCULAR'
order by ti.lin_extensao desc
limit 1

--------------------------------------------------------------------------------------------------------

**I. Quais linhas tem tarifa de R$5,50 ?**

select
	tx_linha,vl_tarifa
from 
	dados_mobilidade.tab_linha
where vl_tarifa = 5.50

--------------------------------------------------------------------------------------------------------

**J. Quais linhas são operadas pela empresa piracicabana ?**

select
    tl.cd_linha,
	tl.tx_linha,
    t.nm_operadora
from
    dados_mobilidade.tab_linha tl 
join 
	dados_mobilidade.tab_itinerario ti
   on ti.id_linha = tl.id_linha
join
	dados_mobilidade.tab_operadora_linha tol
	on tl.id_linha = tol.id_linha
join
	dados_mobilidade.tab_operadora t
	on tol.id_operadora = t.id_operadora
where t.nm_operadora like ('%PIRACICABANA%')

--------------------------------------------------------------------------------------------------------

**K. Qual a média das extensões das linhas da empresa pioneira ?**
 
SELECT
    AVG(ti.lin_extensao) AS media_geral_pioneira
FROM
    dados_mobilidade.tab_linha tl 
JOIN 
    dados_mobilidade.tab_itinerario ti
    ON ti.id_linha = tl.id_linha
JOIN
    dados_mobilidade.tab_operadora_linha tol
    ON tl.id_linha = tol.id_linha
JOIN
    dados_mobilidade.tab_operadora t
    ON tol.id_operadora = t.id_operadora
WHERE
    t.nm_operadora LIKE '%PIONEIRA%';

--------------------------------------------------------------------------------------------------------

**I. Quais linhas no nome tem 'Gama' ?**

SELECT 
    tx_linha
FROM 
    dados_mobilidade.tab_linha
WHERE 
    tx_linha ILIKE '%GAMA%';

--------------------------------------------------------------------------------------------------------

**M. Quais linhas são compartilhadas ?**

SELECT 
    tl.cd_linha, 
    tl.tx_linha, 
    COUNT(tol.id_operadora) AS qtd_operadoras
FROM 
    dados_mobilidade.tab_linha tl
JOIN 
    dados_mobilidade.tab_operadora_linha tol 
    ON tl.id_linha = tol.id_linha
GROUP BY 
    tl.cd_linha, 
    tl.tx_linha
HAVING 
    COUNT(tol.id_operadora) > 1;

--------------------------------------------------------------------------------------------------------

**N. Qual o itinerário da linha 0.170 no sentido de ida e qual itinerário no sentido de volta ?**

select ti.geo_linhas_lin,
    tl.cd_linha,
    ti.lin_sentido,
    ti.id_itinerario
FROM 
    dados_mobilidade.tab_itinerario ti
JOIN 
    dados_mobilidade.tab_linha tl
    ON ti.id_linha = tl.id_linha
WHERE 
    tl.cd_linha = '0.170'
ORDER BY 
    ti.lin_sentido;

--------------------------------------------------------------------------------------------------------

**O. Considerando apenas o texto do itinerário, quais linhas passam pelo eixo monumental ?**

SELECT 
    tl.cd_linha, 
    tl.tx_linha 
FROM 
    dados_mobilidade.tab_linha tl
WHERE
    tl.tx_linha ilike '%Eixo Monumental%';

--------------------------------------------------------------------------------------------------------

**P. Por quantos trechos do itinerário passa a linha 0.111 ?**

SELECT 
    tl.cd_linha, 
    tl.tx_linha,
    (LENGTH(tx_linha) - LENGTH(REPLACE(tx_linha, '/', '')) + 1) AS total_trechos
FROM 
    dados_mobilidade.tab_linha tl
WHERE 
    tl.cd_linha = '0.111';

--------------------------------------------------------------------------------------------------------

**Q. Quais linhas serão ativadas nos próximos dias ?**

--------------------------------------------------------------------------------------------------------

**R. Qual a tabela de horário de domingo da linha 0.763 ?**

select
	tl.cd_linha,
	th.hr_prevista,
	th.domingo
from
	dados_mobilidade.tab_horario th
join 
	dados_mobilidade.tab_operadora_linha tol
	on th.id_operadora_linha = tol.id_operadora_linha
join
	dados_mobilidade.tab_linha tl
	on tol.id_linha = tl.id_linha
where
	tl.cd_linha = '0.763'
	
--------------------------------------------------------------------------------------------------------

**S. Quantas viagens são programadas aos sábados para cada empresa ?**

select 
	count(th.sabado),
	t.nm_operadora
from 
	dados_mobilidade.tab_horario th
join 
    dados_mobilidade.tab_operadora_linha tol
	on tol.id_operadora_linha = th.id_operadora_linha
join
    dados_mobilidade.tab_operadora t
    on t.id_operadora = tol.id_operadora
where 
    th.sabado ilike 'S'
group by
    th.sabado,
    t.nm_operadora
	
--------------------------------------------------------------------------------------------------------

**T. Qual linha tem mais viagens programadas ?**

   select
	tvr.linha,
	COUNT(tvr.viagem) as qtd
from 
	dados_mobilidade.tab_viagens_realizada tvr 
group by
	tvr.linha,
	tvr.viagem
order by qtd desc
limit 
	1
    
--------------------------------------------------------------------------------------------------------

**U. Qual linhas tem duração de viagem mais longa ?**

select
	tl.cd_linha,
	max(th.tempo_percurso) as viagem_mais_longa
from
	dados_mobilidade.tab_horario th
join
	dados_mobilidade.tab_operadora_linha tol
	on tol.id_operadora_linha = th.id_operadora_linha
join
	dados_mobilidade.tab_linha tl
	on tl.id_linha =  tol.id_linha
group by
    tl.cd_linha
order by
	viagem_mais_longa desc
limit
	1;

--------------------------------------------------------------------------------------------------------

**V. Quais linhas operam de madrugada (entre 00h 4h00) ?**

select
	tl.cd_linha,
	tl.tx_linha,
	th.hr_prevista
from 
    dados_mobilidade.tab_horario th
join 
    dados_mobilidade.tab_operadora_linha t
    on th.id_operadora_linha = t.id_operadora_linha 
join
	dados_mobilidade.tab_linha tl
	on t.id_linha = tl.id_linha
where
	th.hr_prevista between 0 and 4
group by
	tl.cd_linha,
	tl.tx_linha,
	th.hr_prevista
order by
    th.hr_prevista desc

--------------------------------------------------------------------------------------------------------










**Consultas Espaciais e Georeferenciamento (SQL/Postgis)**



**A. O que é a extensão postgis ?**

--------------------------------------------------------------------------------------------------------

**B. Quais paradas de ônibus que servem à linha 0.763, por sentido ?**

select 
    p.id AS parada_id, 
    p.cod_dftrans, 
    i.lin_sentido,
    p.geom_parada
FROM 
    dados_mobilidade.tab_parada p
JOIN 
    dados_mobilidade.tab_itinerario i 
    ON ST_DWithin(p.geom_parada, i.geo_linhas_lin, 0.0005)
JOIN 
    dados_mobilidade.tab_linha l 
    ON i.id_linha = l.id_linha
WHERE 
    l.cd_linha = '0.763'
ORDER BY 
    i.lin_sentido, 
    ST_LineLocatePoint(i.geo_linhas_lin, p.geom_parada);

--------------------------------------------------------------------------------------------------------

**C. Quais são as paradas de ônibus por região administrativa ?**

WITH ra_preparada AS (
    SELECT 
        dsc_regiao_administrativa,
        ST_Transform(geo_regiao_administrativa, (SELECT ST_SRID(geom_parada) FROM dados_mobilidade.tab_parada LIMIT 1)) AS geom_ajustada
    FROM 
        bdf.tab_regioes_administrativas
)
SELECT 
    COALESCE(ra.dsc_regiao_administrativa, 'TOTAL GERAL') AS regiao_administrativa, 
    COUNT(p.id) AS total_paradas
FROM 
    ra_preparada ra
LEFT JOIN 
    dados_mobilidade.tab_parada p 
    ON ST_Intersects(p.geom_parada, ra.geom_ajustada)
GROUP BY 
    ROLLUP(ra.dsc_regiao_administrativa)
ORDER BY 
    total_paradas ASC;

                       **OU**
                       
WITH ra_preparada AS (
    SELECT 
        dsc_regiao_administrativa,
        ST_Transform(geo_regiao_administrativa, (SELECT ST_SRID(geom_parada) FROM dados_mobilidade.tab_parada LIMIT 1)) AS geom_ajustada
    FROM 
        bdf.tab_regioes_administrativas
)
SELECT 
    ra.dsc_regiao_administrativa AS regiao_administrativa, 
    COUNT(p.id) AS total_paradas
FROM 
    ra_preparada ra
LEFT JOIN 
    dados_mobilidade.tab_parada p 
    ON ST_Intersects(p.geom_parada, ra.geom_ajustada)
GROUP BY 
    ra.dsc_regiao_administrativa
ORDER BY 
    total_paradas DESC;

--------------------------------------------------------------------------------------------------------

**D. Quais linhas de ônibus passam por faixa exclusiva ?**

--------------------------------------------------------------------------------------------------------

**E. Quais linhas de ônibus passam pela faixa exclusiva da W3 ?**

--------------------------------------------------------------------------------------------------------

**F. Quais veículos, entre 10h e 11h da manhã do último dia útil, operaram na linha 0.170 ?**

--------------------------------------------------------------------------------------------------------

**G. Teve alguma fuga de rota entre 16h e 17h de ontem ?**

--------------------------------------------------------------------------------------------------------

**H. Quais foram as catracadas de passageiros entre 12h e 13h na parada de ônibus do setor hospitalar sul na última data disponível ?**

--------------------------------------------------------------------------------------------------------

**I. Quantas pessoas fizeram acesso sete dias atras no terminal do gama ?**

