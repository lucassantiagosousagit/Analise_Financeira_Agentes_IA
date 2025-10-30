# Aplicativo de Estudos de Tickets Internacionais usando Agentes de IA

[![Python](https://img.shields.io/badge/Python-3.12%2B-blue)](https://www.python.org/)
[![Streamlit](https://img.shields.io/badge/Streamlit-1.x-red)](https://streamlit.io/)
[![Plotly](https://img.shields.io/badge/Plotly-graphing-lightgrey)](https://plotly.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

<details>
<summary><strong>📚 Sumário</strong> (clique para expandir)</summary>

- [Visão Geral](#visão-geral)
- [Arquitetura & Fluxo](#arquitetura--fluxo)
- [Funcionalidades](#funcionalidades)
- [Mapeamento: Código → Funcionalidade](#mapeamento-código--funcionalidade)
- [Como Executar](#como-executar)
- [Uso Rápido](#uso-rápido)
- [Variáveis de Ambiente](#variáveis-de-ambiente)
- [Exemplos de Comando](#exemplos-de-comando)
- [Checklist de Validação](#checklist-de-validação)
- [Roadmap](#roadmap)
- [FAQ](#faq)
- [Licença](#licença)
</details>

---

## Visão Geral

Aplicativo **Streamlit** para estudo de *Day Trade Analytics*. Ele coleta preços históricos de ações via **Yahoo Finance**, gera visualizações interativas com **Plotly** e aciona **agentes de IA** (web search + finanças) para sumarizar notícias e recomendações de analistas em tempo quase real.  
Código base principal: `Estudo_DayTrade.py` fileciteturn1file0. Instruções originais foram adaptadas do `README.txt` enviado nos anexos fileciteturn1file1.

---

## Arquitetura & Fluxo

## Funcionalidades

- **Coleta de dados** (Yahoo Finance): histórico por ticker e período.
- **Visualizações interativas**: linha (preço), candlestick, médias móveis (SMA/EMA) e volume (Plotly).
- **Equipe de Agentes de IA**:
  - **Busca Web** (DuckDuckGo) — sempre retornando **fontes**.
  - **Análise Financeira** (YFinanceTools) — preço, fundamentos, recomendações, notícias.
- **UI em Streamlit** com input de ticker e *spinner* durante processamento.

---

## Mapeamento: Código → Funcionalidade

> Arquivo principal: `Estudo_DayTrade.py` (trechos e nomes abaixo) fileciteturn1file0

| Seção | Função/Bloco | O que faz |
|---|---|---|
| Cache & Dados | `@st.cache_data(ttl=3600)` + `extrai_dados(ticker, period="1mo")` | Baixa histórico com `yfinance.Ticker(...).history(...)`, reseta índice e retorna `DataFrame`. |
| Gráficos | `plot_stock_price(hist, ticker)` | Linha do **Close** com `px.line`, exibe via `st.plotly_chart`. |
| Gráficos | `plot_candlestick(hist, ticker)` | Gráfico de **candlestick** com `go.Candlestick`. |
| Gráficos | `plot_media_movel(hist, ticker)` | Calcula **SMA_20** e **EMA_20**, plota série múltipla com `px.line`. |
| Gráficos | `plot_volume(hist, ticker)` | Barras de **Volume** com `px.bar`. |
| Agentes | `agente_web_search` | DuckDuckGo; instrução “Sempre inclua as fontes”; `show_tool_calls=True`. |
| Agentes | `agente_financeiro` | `YFinanceTools` com preço, recomendações, fundamentos e notícias. |
| Orquestração | `multi_ai_agent` | Time: web + financeiro, prompt conjunto, retorna resumo e fontes. |
| UI | `st.set_page_config(...)`, sidebar, `st.title`, `st.header` | Estrutura da página e instruções. |
| Execução | `if st.button("Analisar"):` | Valida ticker, roda `extrai_dados`, `multi_ai_agent`, faz limpeza de “Running:” por regex e plota todas as visões. |

---

## Como Executar

> [!TIP]
> Você pode usar **conda** (como no documento original) ou **venv**. Abaixo ambos.

### Opção A — `venv` (recomendado para começar)
```bash
# 1) Ambiente
python -m venv .venv
# Windows
.\.venv\Scripts\activate
# macOS/Linux
# source .venv/bin/activate

# 2) Dependências
pip install -r requirements.txt

# 3) Variáveis de ambiente (opcional para agentes IA)
# Crie .env na raiz com, por exemplo:
# GROQ_API_KEY=seu_token_aqui

# 4) Executar
streamlit run Estudo_DayTrade.py
```

### Opção B — `conda`
```bash
conda create -n daytrade python=3.12
conda activate daytrade
pip install -r requirements.txt
streamlit run Estudo_DayTrade.py
```

> [!WARNING]
> Sem `GROQ_API_KEY`, os agentes de IA podem não funcionar. As visualizações de preço via Yahoo Finance rodam normalmente. Para criação da chave, vá em https://console.groq.com/keys e crie uma chave. Copie o código gerado e cole no arquivo .env do projeto seguindo o layout GROQ_API_KEY = "valor do código gerado na API key"

---

## Uso Rápido

1. Abra o app em `http://localhost:8501` após executar o comando acima.
2. Informe um **ticker** (ex.: `MSFT`, `TSLA`, `AMZN`, `GOOG`).  
3. Clique em **Analisar**.
4. Veja:
   - **Resumo do agente** (notícias + recomendações, com **fontes**).
   - **Gráficos**: preço, candlestick, médias (SMA/EMA) e volume.

> [!NOTE]
> A página usa `st.spinner` e `cache_data` para melhorar fluidez e evitar reprocesso excessivo.

---

## Variáveis de Ambiente

Crie um arquivo `.env` na raiz para as chaves dos modelos/serviços (se necessário):
```env
GROQ_API_KEY=seu_token_aqui
```

---

## Exemplos de Comando

```bash
# Rodar
streamlit run Estudo_DayTrade.py

# Congelar dependências (opcional, para reprodução exata)
pip freeze > requirements-lock.txt
```

---

## Checklist de Validação

- [ ] `pip install -r requirements.txt` executa sem erros
- [ ] `streamlit run Estudo_DayTrade.py` abre no navegador
- [ ] Input de ticker (`AAPL`, `MSFT`, etc.) retorna gráficos
- [ ] Agentes de IA respondem com **fontes** visíveis no markdown
