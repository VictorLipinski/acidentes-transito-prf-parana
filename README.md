# Caracterização das ocorrências e panorama da segurança viária no Paraná

## Visão geral

Este projeto consiste em uma análise acadêmica de acidentes de trânsito nas rodovias federais do Paraná, com base em dados públicos do Datatran da PRF. O objetivo principal é transformar dados brutos em uma estrutura analítica consistente, validada e pronta para uso em dashboard, com foco em indicadores de segurança viária, distribuição geográfica e padrões temporais.

A solução foi desenvolvida em Python, com tratamento, validação, modelagem dimensional e exportação de uma base final para visualização em BI. O repositório preserva os arquivos originais em CSV, bem como os dados processados gerados pela pipeline.

## 1. Contexto acadêmico e objetivo

O projeto foi conduzido como uma iniciativa de análise de dados aplicada ao tema de segurança viária, com ênfase em dados públicos e em metodologias de estruturação analítica para BI. A abordagem adotada prioriza consistência, clareza metodológica e validação da granularidade antes da geração da base final.

O objetivo do trabalho é:

- analisar a ocorrência de acidentes de trânsito no Paraná;
- identificar padrões por município, BR, fase do dia, dia da semana e condições meteorológicas;
- validar a consistência dos dados antes de qualquer agregação;
- preparar uma base estruturada para visualização em dashboard;
- apoiar a interpretação dos indicadores de segurança viária por meio de uma representação visual analítica.

## 2. Fonte dos dados e período

A base de origem está localizada no diretório `bases/` e contém arquivos CSV anuais, separados por `;` e codificados em `latin1`.

Período analisado:

- 2023
- 2024
- 2025
- 2026

Arquivos disponíveis:

- `bases/acidentes2023.csv`
- `bases/acidentes2024.csv`
- `bases/acidentes2025.csv`
- `bases/acidentes2026.csv`

Os dados foram filtrados para registros no estado do Paraná, conforme a regra validada no código: `uf == "PR"`.

Importante: os dados são públicos e foram disponibilizados pela PRF por meio do DATATran. A estrutura do projeto preserva os arquivos originais sem alteração, garantindo rastreabilidade e reprodutibilidade do processo.

## 3. Metodologia de tratamento e análise

A pipeline foi organizada em etapas sequenciais, com foco em validação e consistência antes da modelagem final.

### 3.1 Inspeção das bases

O script `scripts/01_inspecao_bases.py` realiza:

- localização dos arquivos CSV de acidentes;
- checagem dos anos disponíveis;
- validação da estrutura das colunas em cada base;
- padronização de nomes e tipos básicos;
- concatenação das bases por ano;
- filtro inicial para registros do Paraná;
- verificação de ausência de linhas duplicadas completas;
- validação da granularidade e da consistência das flags de condição das pessoas.

### 3.2 Granularidade e qualidade dos dados

Uma parte central da metodologia foi garantir que a agregação não multiplicasse registros indevidamente.

A granularidade validada no projeto foi:

- Acidente: `(ano_fonte, id)`
- Pessoa: `(ano_fonte, id, pesid)`
- Veículo: `(ano_fonte, id, id_veiculo)`

Esse cuidado foi necessário porque a base original não aparece como uma tabela de um registro por acidente. A soma direta de linhas sem validação de granularidade pode gerar contagens inconsistentes.

### 3.3 Modelagem dimensional

O script `scripts/02_modelagem_dimensional.py` implementa a modelagem dimensional do projeto, produzindo:

- `f_acidentes`: tabela fato central
- `d_tempo`: dimensão temporal
- `d_localidade`: dimensão geográfica/localização
- `d_circunstancia`: dimensão de circunstâncias do acidente

A estrutura foi montada para manter uma linha por acidente no fato, com associações por chaves substitutas (`sk_*`), evitando duplicação de registros em um cenário de muitos-para-um.

### 3.4 Base final para dashboard

O script `scripts/03_base_plana_data_studio.py` consolida a base em formato apropriado para exploração em dashboard, garantindo que:

- a tabela final tenha uma linha por acidente;
- os indicadores de vítimas e severidade permaneçam consistentes;
- as dimensões de tempo, localização e circunstância sejam combinadas ao fato;
- a base seja exportada em formato CSV pronto para importação no Data Studio.

## 4. Tecnologias e ferramentas utilizadas

O projeto foi implementado com as tecnologias e bibliotecas presentes no repositório:

- Python
- Pandas
- pathlib
- glob
- processamento sequencial em células marcadas com `# %%`
- arquivos CSV como fonte principal
- Google Data Studio para visualização analítica a partir de arquivo CSV importado
- exportação em SVG para mockups/representações visuais no diretório `dashboard_visual/`

Dependências declaradas em `requirements.txt`:

- `pandas>=2.2,<4.0`
- `ipykernel>=6.29,<8.0`

## 5. Estrutura real do repositório

A estrutura atual do repositório é a seguinte:

```text
final/
├── .gitignore
├── .venv/
├── requirements.txt
├── bases/
│   ├── acidentes2023.csv
│   ├── acidentes2024.csv
│   ├── acidentes2025.csv
│   └── acidentes2026.csv
├── data/
│   └── processed/
│       ├── base_acidentes_dashboard.csv
│       ├── d_circunstancia.csv
│       ├── d_localidade.csv
│       ├── d_tempo.csv
│       └── f_acidentes.csv
├── dashboard_visual/
│   ├── PRF - 1 dash.svg
│   ├── tela_ocorrências.svg
│   └── tela_panorama.svg
├── scripts/
│   ├── 01_inspecao_bases.py
│   ├── 02_modelagem_dimensional.py
│   └── 03_base_plana_data_studio.py
└── README.md
```

## 6. Resultados e indicadores encontrados

Os indicadores abaixo foram calculados a partir da base processada disponível no repositório em `data/processed/base_acidentes_dashboard.csv`.

### 6.1 Indicadores principais

- Total de acidentes: 25.990
- Pessoas identificadas: 64.779
- Pessoas ilesas: 29.706
- Feridos leves: 22.299
- Feridos graves: 6.664
- Óbitos: 2.035

### 6.2 Distribuição geográfica e temporal

A base mostra maior concentração de ocorrências em municípios como:

- Curitiba
- São José dos Pinhais
- Cascavel
- Maringá
- Ponta Grossa
- Campina Grande do Sul
- Foz do Iguaçu
- Paranaguá
- Londrina
- Apucarana

Ainda que o foco do projeto seja a análise de segurança viária e a preparação de uma base para dashboard, a distribuição dos acidentes indica relevância em regiões metropolitanas e eixos de maior circulação no estado.

## 7. Dashboard e páginas de visualização

O dashboard foi estruturado em duas páginas principais e está disponível em Data Studio, com a base exportada em CSV para importação no link abaixo:

https://datastudio.google.com/reporting/61abec7a-69b7-4d8a-a065-3cfba72f15b5

### Página 1: Caracterização das ocorrências

Esta página foi pensada para apresentar:

- indicadores de quantidade de acidentes;
- consolidação por município, BR, regional e unidade operacional;
- distribuição por fase do dia, dia da semana e mês;
- indicadores de gravidade e classificação do acidente;
- estrutura de monitoramento e acompanhamento operacional.

### Página 2: Panorama da segurança viária

A segunda página foi concebida para oferecer:

- visão geral da situação da segurança viária;
- análise comparativa dos principais indicadores;
- contexto de severidade e exposição;
- análises exploratórias por período, região e tipo de ocorrência;
- suporte à interpretação de padrões e tendências.

Os arquivos SVG em `dashboard_visual/` representam mockups visuais do dashboard, criados em Figma e exportados para a pasta do projeto.

## 8. Limitações metodológicas

O projeto apresenta algumas limitações que devem ser reconhecidas para uma leitura técnica adequada:

- os dados são públicos e brutos, exigindo padronização e validação antes da análise;
- a análise foi restringida ao Paraná, sem intenção de extrapolar os resultados para outros estados;
- a estrutura foi desenvolvida com foco em clareza, consistência e uso em dashboard, e não em uma arquitetura complexa de dados;
- a base original exige atenção à granularidade para evitar contagem indevida de registros;
- a ausência de documentação complementar da fonte limita interpretações contextuais mais profundas sobre eventos e contexto operacional;
- o projeto não inclui integração com outras fontes externas nem modelagem estatística avançada.

## 9. Instruções de reprodução do projeto

### 9.1 Pré-requisitos

- Python 3.x
- ambiente virtual recomendado
- acesso ao repositório local

### 9.2 Dependências

No diretório do projeto, execute:

```bash
pip install -r requirements.txt
```

### 9.3 Execução dos scripts

Na ordem correta:

```bash
python3 scripts/01_inspecao_bases.py
python3 scripts/02_modelagem_dimensional.py
python3 scripts/03_base_plana_data_studio.py
```

### 9.4 Saídas esperadas

Após a execução, os arquivos processados serão gerados em `data/processed/`:

- `f_acidentes.csv`
- `d_tempo.csv`
- `d_localidade.csv`
- `d_circunstancia.csv`
- `base_acidentes_dashboard.csv`

Esses arquivos constituem a base final para visualização e análise no dashboard.

## 10. Observações finais

Este projeto foi estruturado para manter uma relação direta entre dados originais, tratamento analítico e visualização. A forma como os scripts foram organizados reflete uma lógica de validação, padronização e modelagem dimensional simples, adequada ao contexto acadêmico e ao objetivo de apoiar a leitura dos indicadores de segurança viária.

O repositório preserva tanto a origem dos dados quanto a lógica de transformação aplicada, o que facilita a compreensão do processo e a reprodutibilidade da análise.
# acidentes-transito-prf-parana
