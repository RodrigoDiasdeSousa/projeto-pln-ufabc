# projeto-pln-ufabc

Projeto prático da disciplina de **Processamento de Linguagem Natural (PLN) — UFABC [2026-Q3]** (Prof. Alexandre Donizeti Alves).

## Equipe

- **Integrante 01:** Rodrigo Dias de Sousa — RA 11202231964
- **Integrante 02:** Henrique Pineda Pietrobon Redini Martins — RA 11202230887

## Visão geral

O notebook [Projeto_PLN_RAG.ipynb](Projeto_PLN_RAG.ipynb) combina **LangChain**, **Gemini 3.5 Flash-Lite** (Google) e a **GitHub REST API** para coletar, limpar, analisar e consultar os READMEs de **15 repositórios públicos** balanceados em três áreas:

- **Engenharia de dados:** `dlt-hub/dlt`, `airbytehq/airbyte`, `apache/nifi`, `apache/seatunnel`, `apache/hop`
- **Análise e visualização:** `apache/superset`, `metabase/metabase`, `vega/vega-lite`, `plotly/plotly.py`, `matplotlib/matplotlib`
- **Aprendizado de máquina:** `dmlc/xgboost`, `microsoft/LightGBM`, `catboost/catboost`, `scikit-learn/scikit-learn`, `pytorch/pytorch`

### Técnicas de PLN aplicadas

1. **Sumarização de Textos:** resumo em português (até três frases) destacando objetivo e entrega de cada projeto via LCEL (`ChatPromptTemplate | llm | StrOutputParser`).
2. **Classificação de Textos com Saída Estruturada:** classificação na taxonomia fechada usando `llm.with_structured_output(ClassificacaoProjeto)` (Pydantic), com auditoria automática das evidências (comparação exata e normalizada) e avaliação por acurácia, matriz de confusão e precisão/revocação/F1.
3. **Sistema de Perguntas e Respostas com RAG Híbrido:** pré-processamento dos READMEs (remoção de HTML, badges, licenças comentadas, diretivas RST e URLs — redução média de **50,7%** de ruído), fragmentação com `RecursiveCharacterTextSplitter`, embeddings multilíngues **E5** (`intfloat/multilingual-e5-small`) via `InMemoryVectorStore` combinados com **TF-IDF**, diversificação por repositório (`max_por_repo=2`), métricas de `Recall@k` e teste negativo para controle de alucinação.

## Resultados resumidos

- **Coleta:** 15/15 READMEs (com fallback automático para `raw.githubusercontent.com` em caso de rate limit HTTP 403).
- **Classificação:** **15/15 (100,0%)** de acurácia e **F1-score = 1,00** em todas as classes; **15/15** rótulos válidos e citações válidas normalizadas.
- **RAG Híbrido:** `Recall@10` de **100%** em *boosting* e *conectores/ELT*, **67%** em *dashboards interativos*, e **zero alucinação** no teste negativo fora do escopo (*Qiskit*).

## Como executar

### No Google Colab
1. Abra `Projeto_PLN_RAG.ipynb` no Google Colab.
2. Em **Secrets** (ícone de chave na barra lateral), adicione:
   - `key_1`: sua chave da Gemini Developer API (obrigatório).
   - `GITHUB_TOKEN`: seu token do GitHub (opcional, evita limite de 60 req/h em IPs compartilhados).
3. Execute o notebook sequencialmente (*Ambiente de execução → Executar tudo*).

### Localmente
```bash
python -m venv .venv
# Windows (PowerShell):
.venv\Scripts\Activate.ps1
# Linux/macOS:
# source .venv/bin/activate

pip install -r requirements.txt
export GOOGLE_API_KEY="sua-chave-gemini"   # ou $env:GOOGLE_API_KEY="..." no PowerShell
jupyter lab Projeto_PLN_RAG.ipynb
```
