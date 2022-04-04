# Processamento_Automático_de_Invoices_a_partir_de_Arquivos_PDF

#### Aluno: [Edson Andrade de Moraes](https://github.com/EdsonAndMor/)
#### Orientador: [Leonardo Alfredo Forero Mendonza](https://github.com/leofome8)
---

Trabalho apresentado ao curso [BI MASTER - Turma 2019-004](https://ica.puc-rio.ai/bi-master) como pré-requisito para conclusão de curso e obtenção de crédito na disciplina "Projetos de Sistemas Inteligentes de Apoio à Decisão".

- [Link para o código](https://github.com/EdsonAndMor/invoi_proc). 

---

### Resumo

Esta prova de conceito implementa uma solução pra extração de informações comerciais de um documento fiscal de comércio exterior conhecido como invoice. O objetivo é extrair as informações mais relevantes do cabeçalho do documento bem como todo o detalhamento dos itens que estão sendo comercializados. Com um médio de 51 % das informações de todos os documentos utilizados como teste, os resultados foram considerados promissores para a construção de uma solução mais completa. Indicando que é possível criar solução, mesmo que otimizada para alguns modelos de invoice mais comuns, com uma alta taxa de extração de informações. Possibilitando assim uma automação do processo de escrituração unicamente a partir destes documentos.

### 1. Introdução

O objetivo desta prova de conceito é implementar uma proposta para a extração de informações contidas em um documento conhecido como invoice. Trata-se do documento que inicia uma negociação no Comércio Exterior. Seus dados são utilizados para a emissão dos demais documentos fiscais exigidos na legislação brasileira, bem como para registro de informações de natureza contábil, financeira e logística em uma companhia. Entretanto, apesar de sua importância, este documento não possui um padrão de uso no qual constem as informações obrigatórias. Ou mesmo um leiaute padronizado da sua disposição gráfica.  Configurando assim um cenário para aplicação de técnicas de ciência de dados para extrair as informações relevantes deste documento com vistas a automatizar processos. Para esta POC previmos apenas a possibilidade de documentos na língua inglesa(casos mais comuns).
Decidimos buscar as seguintes informações a partir dos dados de cabeçalho documento:
- Número da invoice.
- Comprador do produto.
- Consignatário (responsável pela guarda do produto até que ele seja entregue ao cliente)
- FOB (termo comercial que se refere ao preço do produto sem acréscimo de frete e seguro)
- Frete.
- Seguro.
- Valor total da invoice
- Data de missão da invoice
- Companhia emitente da invoice.

Quanto as informações de detalhamento dos itens da invoice, entendemos ser adequado trazer o máximo de informação possível. Pois, na maioria dos casos, estas informações vêm organizadas em tabelas dentro do documento da invoice. Com destaque para as informações de preço unitário do item, preço total do item, quantidade e descrição do item. Com estes objetivos em vista, foi programado um notebook na ferramenta Google Colaboratory, utilizando a linguagem Python, para extrair estas informações de um documento de invoice. 


### 2. Modelagem

O notebook foi criado com a premissa que as invoices seriam alimentadas em padrão PDF, o mais comum para envio de documentos desse tipo. O notebook segue as seguintes etapas de processamento:
- Leitura do arquivo PDF e conversão do mesmo para uma imagem.
- Utilização de técnica OCR para obtenção de todos os textos que estão presentes na imagem. E sua respectiva posição no documento.
- Criação de uma segunda imagem onde os textos são aplicados, sem qualquer elemento de formatação como linhas ou caixas de texto. Adotou-se essa abordagem, pois após sucessivas tentativas identificou-se que esses elementos apesar de aparentemente serem uteis para entender a estrutura do documento, poderiam vir falhados e necessitar de tratamentos adicionais para serem corrigidos. E também, não há garantia que todas as invoices possuem linhas ou caixas de textos que delimitem sua estrutura fazendo com que não se possa generalizar uma abordagem baseada na presença dessas estruturas.
- A imagem obtida (contendo somente os textos) foi dilatada e erodida para em seguida aplicar um algoritmo de detecção de contornos para agrupar os textos. É comum nesse tipo de documento, haver texto tanto com leiaute colunar, como texto com leiaute horizontal.
- Utilizando distância de Levenshtein identifica-se o nome de coluna de quantidade dos itens. E a partir dele identifica-se o grid.
- Uma vez identificado o grid, este é extraído para um arquivo CSV com seus respectivos títulos de coluna e valores.
- Com o resto do documento extraído anteriormente, aglutinam-se os blocos de texto para se comportarem como linhas de texto. E em seguida são aplicadas bibliotecas de processamento de linguagem natural para buscar as outras informações de cabeçalho. E então gravá-las em arquivo CSV.


### 3. Resultados

Foram utilizados 4 documentos para testar a eficácia do notebook gerado. 3 documentos eram invoices reais emitidas por empresas do mercado e uma era um documento gerado nos ambientes de teste da Petrobras com valores fictícios, mas num padrão similar ao que é utilizado pela empresa. Nem todos os documentos possuem todas as informações de cabeçalho que elencamos como mais relevantes. Portanto, considerarmos para efeito de cálculo somente as informações que poderiam efetivamente ser extraídas.

|Variantes	              |% Itens	| Observação	                    |% Cabeçalho |Observação	                             |% Média|
|:----------------------|:-------:|:-----------------------------------|:----------:|:-------------------------------------|:-----:|
|1  - Doc. Real|100%|Exportou todo os dados p/ o CSV	|50%|4 das 8 informações estavam disponíveis<br>no doc. Recuperou 2.|75%|
|2 - Doc. amb. de teste|100%|Exportou todo os dados p/ o CSV	|100%	|8 informações diposniveis. Recuperou 8.  |100%|
|3 - Doc. Real|0%|Itens apresentados em forma quase textual. Fazendo com que a premissa de extrair os itens em um forma de tabela falhasse.|60%|5 de 8 informações estavam disponiveis. 3 foram extraídas.|30%|
|4 - Doc. Real|0%|A formatação muito irregular inviabilizou a extração|0%| A formatação muito irregular inviabilizou a extração|0%|
                                                               

### 4. Conclusões

O resultado médio atingido pela POC foi de 51% de extração das informações desejadas em relação a totalidade os documentos utilizados como teste. Note-se que dois casos atingiram taxas relativamente interessantes de 100% e 75%, e nestes dois casos os documentos possuem formatação completamente diferentes. Seguem as observações sobre os resultados atingidos:
- Utilização de tabelas ou caixas de texto como elementos de estruturação da informação: desde as primeiras versões que foram programadas do notebook, verificamos que a utilização do leiaute como um guia para identificar as informações e a relação entre elas, mostrou-se uma estratégia de pouco retorno. Falhas nas linhas de tabelas e em alguns casos a total ausência de tabelas ou caixas de texto, inviabilizaram uma abordagem mais genérica baseada nestes elementos. 
- Utilização de técnicas de visão computacional: a utilização de visão computacional aplicada apenas sobre os elementos textuais do documento mostrou-se bastante eficaz. Em todos os casos, mostrou-se capaz de melhorar nosso entendimento sobre a estrutura do documento sem depender de elementos gráficos de formatação. Notoriamente, em casos de textos maiores que são impressos em formato colunar. 
- Utilização de técnicas de processamento de linguagem natural:  mostraram-se eficazes na identificação das informações textuais, principalmente de datas.
- Hipóteses para melhoria da solução: utilização mais intensa técnicas de processamento de linguagem natural, com possível utilização de diferentes corpora para identificar melhor as informações a serem extraídas. Notoriamente no caso de invoices que tem uma formatação mais semelhante a texto livre, essas técnicas tem potencial de melhorar o percentual de extração das informações.

---

Pontifícia Universidade Católica do Rio de Janeiro

Curso de Pós Graduação *Business Intelligence Master*

