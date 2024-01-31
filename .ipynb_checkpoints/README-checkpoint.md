# Gourmet

"Olá rapadurinha"

## Primeiro Entregável

![Primeiro Entregável](notebooks/entregavel_001_NSB.ipynb)

## Segundo Entregável

![Segundo Entregável](notebooks/entregavel_002_NSB.ipynb)

## Terceiro Entregável

![](imgs/datalake.png)

### Motivação

Informações advindas de notas fiscais são extremamente importantes e precisam ser gerenciadas de forma profissional, consistente e nos seus mais diversos formatos. Elas são alvo de auditoria interna e externa, representam a peça central das transações financeiras de uma empresa, podem ser utilizadas em diversos tipos diferentes de análises, ou seja, podem ser requisitas por times diferentes e para fins diferentes. Por isso, manter a informação mais inalterada possível em uma  camada pode ser de grande valia visto que diversos times podem as solicitar para para entendimento e rotinas de conferência.

Para isso sugeri uma plataforma de dados, capaz de ingerir dados e os manter inalterados em camada "raw", o que foi construído no entregável passado seria utilizado para transformação das notas (coletando as notas em formato .json da camada 'raw' no GCS) e rotina de armazenamento na zona "curated" (utilizando BQ).

A solução proposta é capaz de cumprir uma série de objetivos:

1. Ela é capaz de executar rotinas simples e complexas de ingestão e transformação de dados;
2. Ela é capaz de armazenar informação inalterada em seus mais diversos formatos (.csv, .xml, .json, imagens e outros arquivos binários);
3. Ela é capaz de armazenar informação de forma relacional pós-transformação para aplicação em relatórios e análises;
4. Ela dá poder ao time de negócio para criar suas próprias análsies de forma self-service, sem a preocupação de alterações permanentes na fonte do dados;
5. Ela possibili

### Estrutura proposta

A arquitetura tem três grandes camadas:

1. Fonte de dados: aqui estão todas as fontes de dados que serão utilizadas e conectadas ao nosso datalake incluindo a fonte das notas fiscais (API), outros banco relacionais, sistemas legados (ERP, CRMs etc) e arquivos binários.  
2. Plataforma de dados: A plataforma começa com rotinas de ingestão feitas através do Cloud Data Fusion; todas as carga vão para uma zona "Raw" onde os dados estão em sua versão mais original e não alterada (essa camada está toda em Google Cloud Storage); utilizando Data Fusion o time é capaz de aplicar regras de négocio e também fazer as transformações necessárias e passar os dados que estavam em zona "Raw" para uma zona "Curated".
A zona "Curated" possui dados aplicados ao negócio (tabelas de KPIs, cruzamento de dados) e também pode ser utilizada como feature store para modelos de machine learning; os dados em zona "Raw" e em zona "Curated" são catalogadas através do Data Catalog, esse catálogo pode ser disponibilizado para toda a empresa e tem a principal função de dar publicidade aos dados coletados. Além disso, habilitando o StackDriver é possível coletar, gerenciar e monitorar todos os logs da plataforma (e seus jobs de ingestão de transformação de dados), incluindo latência, volume de carga entre outros KPIs importantes para gerenciamento de custo. Por fim, o Cloud Identity fornece uma foram simples e rápida de gerenciar acessos aos dados e pode ser associado a regras de segurança de dados para garantir a integridade de segurança da informação (só quem precisa pode ter acesso a dados e tudo é logado para auditoria futura).  
3. Camada analítica: A camada analítica utiliza os dados da zona "Curated" e ferramentas como o Vertex AI para criação de pipelines de treinamento contínuo de modelos de machine learning (e sua disponibilização), além de oferecer ferramentas de Business Intelligence como o Looker para construção de relatórios. Também existe a possibilidade da conexão entre zona "curated" e ferramentas de BI de outros parceiros como PowerBI e Tableau.

#### Ferramental utilizado

* Cloud Data Fusion: ambiente gerenciado para criação e execução de pipelines de ingestão de dados, escalável, já possui integração nativa com schedulers. Também oferece um conjunto de conectores padrão para diversas fontes e situações. Aumenta a velocidade de desenvolvimento e reduz tempo de manutenção.
* Google Data Storage: ambiente para armazenamento de arquivos BLOBs, com capacidade de versionamento de arquivos e também de configuração de rotinas de retenção de informação e prevenção de deleção de arquivos. 
* Google BigQuery: ferramental utilizado para construção e acesso a bases de dados "quentes" e constantemente utilizados pelo time analítico. Ofere uma interface simples para gestão de bases e editor de queries SQL para trabalhos de exploração. Em casos de bases sem necessidade de acesso constante é recomendado o uso de outras ferramentas (como o próprio Google Cloud Storage e CloudSQL)
* Data Catalog: ferramental para armazenamento e manutenção de metadados das bases oferecidas em zonas "Raw" e "Curated", oferece uma visualização unificada para pesquisa de dados e também auxilia oferecendo uma gestão de acesso a nível colunar para o BigQuery.
* Google Stackdriver: ferramental utilizado para gerenciamento e análise de logs em tempo real, composto pela junção do cloud monitoring e cloud logging. O cloud logging nos dá a possibilidade de criar métricas baseadas em logs e explorar todos os logs dos serviços habilitados, já o cloud monitoring vai nos dar uma interface simples para dashboards de logs e também um sistema de alarme. 
* Cloud Identity: o cloud identity já é um serviço habilitado em várias ferramentas citadas acima, ele nos dá a capacidade de gerenciar os usuários com acesso aos serviços da nossa aplicação e garante (se bem configurado) que as pessoas certas terão acesso ao que precisam para construir e manter a infraestrutura construída.
* Vertex AI: ambiente gerenciado para orquestração de pipelines de treinamento de modelos de machine learning, gerenciamento de versões de modelos (model registry), provisonamento de VMs para utilização de jupyter notebook, disponibilização rápida de modelos através de APIs, ferramentas integradas para detecção de drift e agendamento de retreinamento.   
* Looker: ferramenta para construção de visualizações, relatórios e dashboards com fácil integração em ambiente web, integração rápida com serviços como BQ e GCS e possui uma biblioteca de conectores padrão que podem ser utilizados para conexão com fontes externas de dados.
* Outras ferramentas de BI

### Vantagens da estrutura proposta

* Elasticidade Automática: A Capacidade de escalar recursos de forma automática e em tempo real, dependendo da demanda de carga de trabalho.
* Gestão da Infraestrutura: O GCP gerencia a infraestrutura, incluindo servidores, redes e sistemas operacionais.
* Modelo de Pagamento por Uso: Os custos são baseados no consumo real de recursos, sem a necessidade de investimento em hardware.
* Integração com Ferramentas de Análise e BI: Facilidade de integração com ferramentas de Business Inteligence (BI) e análise de Dados
* Custo-Eficiência: Reduz custos operacionais e de Capital, pois não há necessidade de comprar e manter hardware.
* Flexibilidade e Excalabilidade: Permite escalar recursos rapidamente para lidar com picos de demanda sem interrupções ou atrasos.
* Manutenção e Atualizações Automatizadas: A GCP cuida da manutenção e das atualizações, garantindo que o sistema esteja sempre atualizado e seguro.
* Foco no Uso dos Dados: Os usuários podem se concentrar na análise e no uso dos dados, em vez de se preocuparem com questões de infraestrutura.
* Tempo de Implementação Reduzido: Permite uma implementação mais rápida de soluções de armazenamento e analise de dados.
* Acessibilidade: Facilita o acesso a poderosas capacidades de armazenamento e processamento de dados para empresas de todos os tamanhos.

## Observações

* Primeiro entregável: com mais tempo seria capaz de testar mais hipóteses quanto a integridade não nulidade dos dados, perfomar análise de dados para encontro de anomalias em distribuições, definir distribuições baseline para definição de drift no futuro e também a utilização de Great Expectations + Kedro para análise automática e recorrente dos dados e criação de relatório.
* Segundo entregável: com mais tempo teria a capacidade de modularizar o código construído e a rotina para carga no BQ. Além disso, também seria capaz de construir uma análise simples de cesta para identificar padrões de consumo de items (com mais dados) e também construção de relatório.
* Terceiro entregável: com mais tempo teria a capacidade de pensar em estrutura mais simples e outras opções de arquitetura, dimensionamento de time e também cronograma para desenvolvimento e entrega.
