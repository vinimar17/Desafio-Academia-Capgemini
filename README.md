# Desafio-Academia-Capgemini
Parte 2 - Desafio de Programação - Academia Capgemini
Nome: Marcus Vinicius Martins Naves

Ao trabalhar na parte 2 deste desafio, me deparei com algumas dificuldades, pois não tenho experiência com esse tipo de problema. Aqui relatarei como eu faria a minha solução, conforme o entendimento que eu tive do que é solicitado pelo desafio.

Como é um sistema de cadastro de anúncios, considerei o armazenamento das informações em um banco de dados, de modo a inserir os dados do anúncio solicitados no desafio (nome do anúncio, nome do cliente, data de início, data de término e investimento por dia) e os dados dos relatórios (valor total investido, quantidade máxima de visualizações, quantidade máxima de cliques e quantidade máxima de compartilhamentos). 

O valor total investido em um determinado anúncio pode ser determinado na query SQL utilizando-se o valor investido por dia e multiplicando-se pela diferença, em dias, entre a data de término e a data de início do anúncio. Com esse valor em mãos, utiliza-se o programa "calculadora.py" desenvolvido para a Parte 1 desde Desafio para a obtenção dos valores de quantidade máxima de visualizações, quantidade máxima de cliques e quantidade máxima de compartilhamentos.

Segue a query SQL para o desenvolvimento do banco de dados proposto:

CREATE DATABASE cadastro_anuncios

DEFAULT CHARSET utf8

DEFAULT COLLATE utf8_general_ci;

USE cadastro_anuncios;

CREATE TABLE anuncios(id INT NOT NULL PRIMARY KEY AUTO_INCREMENT, anuncio VARCHAR(30), cliente VARCHAR(30), inicio DATE, termino DATE, invest DECIMAL(5,2);

CREATE TABLE relatorios(id INT NOT NULL PRIMARY KEY AUTO_INCREMENT, anuncio_id INT, valortotal DECIMAL(5,2), max_vis INT, max_cliques INT, max_comp INT);

ALTER TABLE relatorios ADD CONSTRAINT FK_anuncios_relatorios FOREIGN KEY (anuncio_id) REFERENCES anuncios(id);

INSERT INTO anuncios (anuncio, cliente, inicio, termino, invest) VALUES('NOME_DO_ANUNCIO1', 'NOME_DO_CLIENTE1', 'AAAA-MM-DD', 'AAA-MM-DD', XX.XX);     # NESTA LINHA SERÃO INSERIDOS OS DADOS DOS ANÚNCIOS QUE SE QUEREM CADASTRAR NO BANCO DE DADOS.

INSERT INTO relatorios (anuncio_id) VALUES(X1);     # NESTA LINHA SERÃO INSERIDOS OS VALORES DA ID DO ANÚNCIO DA TABELA "anuncios", QUE É A CHAVE ESTRANGEIRA DA TABELA "relatorios". ESSA CHAVE É NECESSÁRIA PARA O CÁLCULO DO VALOR TOTAL DO DETERMINADO ANÚNCIO.

UPDATE relatorios, anuncios SET valortotal = invest * (SELECT DATEDIFF(day, inicio, termino)) WHERE anuncio_id = anuncios.id;     # CÁLCULO DO VALOR TOTAL DO DETERMINADO ANÚNCIO.

SELECT valortotal AS Valor_Total_Investido FROM relatorios;     # ESSE COMANDO POSSIBILITA VISUALIZAR O VALOR TOTAL INVESTIDO EM UM DETERMINADO ANÚNCIO. ESSE VALOR SERÁ UTILIZADO NO PROGRAMA "calculadora.py" PARA A OBTENÇÃO DOS VALORES "max_vis" (QUANTIDADE MÁXIMA DE VISUALIZAÇÕES), "max_cliques" (QUANTIDADE MÁXIMA DE CLIQUES) e "max_comp" (QUANTIDADE MÁXIMA DE COMPARTILHAMENTOS). 

INSERT INTO relatorios (max_vis, max_cliques, max_comp) VALUES(X, Y, Z);     # ONDE X, Y, E Z SÃO OS VALORES DE QUANTIDADE MÁXIMA DE VISUALIZAÇÕES, QUANTIDADE MÁXIMA DE CLIQUES E QUANTIDADE MÁXIMA DE COMPARTILHAMENTOS, RESPECTIVAMENTE, OBTIDOS NA UTILIZAÇÃO DO PROGRAMA "calculadora.py".

SELECT * FROM relatorios;     # ESSE COMANDO PERMITE A VISUALIZAÇÃO DO RELATÓRIO DE TODOS OS ANÚNCIOS CADASTRADOS.

SELECT anuncios.cliente AS Nome_do_Cliente, relatorios.valortotal AS Valor_Total_Investido, relatorios.max_vis AS Quantidade_Máxima_de_Visualizações, relatorios.max_cliques AS Quantidade_Máxima_de_Cliques, relatorio.max_comp AS Quantidade_Máxima_de_Compartilhamentos FROM anuncios INNER JOIN relatorios ON anuncios.id = relatorios.anuncio_id WHERE anuncio.cliente = NOME_DO_CLIENTE_DESEJADO;     # ESSE COMANDO PERMITE OBTER O RELATÓRIO DE ANÚNCIOS FILTRADOS PELO NOME DO CLIENTE DESEJADO.

SELECT anuncios.cliente AS Nome_do_Cliente, relatorios.valortotal AS Valor_Total_Investido, relatorios.max_vis AS Quantidade_Máxima_de_Visualizações, relatorios.max_cliques AS Quantidade_Máxima_de_Cliques, relatorio.max_comp AS Quantidade_Máxima_de_Compartilhamentos FROM anuncios INNER JOIN relatorios ON anuncios.id = relatorios.anuncio_id WHERE anuncio.inicio = 'AAAA-MM-DD' AND anuncio.termino = 'AAAA-MM-DD';     # ESSE COMANDO PERMITE OBTER O RELATÓRIO DE ANÚNCIOS FILTRADOS PELAS DATAS DE INÍCIO E TÉRMINO DO ANÚNCIO.

A query SQL acima pode ser implementado no MySQL, por exemplo (foi onde eu o implementei).

Anexo: Script do programa "calculadora.py":

# Entrada: valor investido em reais
valor = float(input('Digite o valor do investimento em reais: '))
while valor < 0:
    valor = float(input('Valor inválido! Digite o valor do investimento em reais: '))

# Visualizações do anúncio original
vis_orig = round(30 * valor)
pessoa0 = vis_orig
clicks = []
comp = []
vis_comp = []

for n in range(0, 3):   # Máximo de 4 compartilhamentos em sequência
    # Quantidade de pessoas que clicam no anúncio
    clicks.append((12 * pessoa0) / 100)
    # Quantidade de pessoas que compartilham o anúncio
    comp.append((3 * clicks[n]) / 20)
    # Visualizações do n-ésimo compartilhamento
    vis_comp.append(comp[n] * 40)
    # Variável 'pessoa0' assume o valor das visualizações do último compartilhamento
    pessoa0 = vis_comp[n]
    # Próximo compartilhamento
    n += 1

# Saída: Projeção da quantidade máxima de pessoas que visualizarão o anúncio

print(f'Quantidade de cliques no anúncio: {round(sum(clicks))}.')
print(f'Quantidade de compartilhamentos: {round(sum(comp))}')
print(f'Projeção de quantidade máxima de pessoas que visualizarão o anúncio:'
      f' {round(vis_orig + sum(vis_comp))} pessoas.')
      
