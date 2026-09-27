# 📦 Validador de Expedição & Separação de Pedidos (Shopee / Bling)

[![Python](https://img.shields.io/badge/Python-3.10+-3776AB.svg?style=flat&logo=python&logoColor=white)](https://www.python.org/)
[![Tkinter](https://img.shields.io/badge/GUI-Tkinter_/_TTK-blue.svg?style=flat)]()
[![FastAPI](https://img.shields.io/badge/Backend-FastAPI-009688.svg?style=flat&logo=fastapi&logoColor=white)](https://fastapi.tiangolo.com/)
[![SQLite](https://img.shields.io/badge/Database-SQLite-003B57.svg?style=flat&logo=sqlite&logoColor=white)](https://sqlite.org/)
[![Status](https://img.shields.io/badge/Operação-Produção-success.svg)]()

> ### 🎯 Contexto & Por que este projeto foi criado
> Em operações de e-commerce de alto volume, especialmente integradas a marketplaces como a **Shopee** e ERPs como o **Bling**, os maiores gargalos e prejuízos operacionais decorrem de **erros humanos na esteira de despacho**.
> Problemas como troca involuntária de pacotes, conferência manual lenta de DANFEs impressos e despachos duplicados geram devoluções caras, reclamações de clientes e penalidades das plataformas.
>
> Este sistema desktop foi concebido e implementado diretamente na bancada de expedição para **automatizar 100% da validação de saída de mercadorias**. Ele realiza o monitoramento contínuo de XMLs de NF-e, integra leitores de código de barras a uma interface de baixa latência (< 50ms), emite alertas sonoros e visuais diferenciados por status e permite que múltiplos computadores trabalhem sincronizados na mesma operação sem conflitos.

---

## 🚦 Fluxo de Status & Sinalização por Cores

Cada Nota Fiscal passa por 3 estágios visuais com preservação contínua na tela:

| Status | Cor / Indicador | Estágio do Pedido | Ação de Bipagem |
|---|---|---|---|
| **Não Bipado** | 🔴 **Vermelho** | XML importado / DANFE emitido | Aguardando 1º Bip de Separação |
| **Em Separação** | 🟡 **Amarelo** | DANFE bipado a 1ª vez | Pedido em separação no estoque |
| **Pronto para Coleta** | 🟢 **Verde** | DANFE bipado a 2ª vez | Pedido conferido, separado e pronto p/ despacho |

*Alerta Sonoro:* Bips curtos e positivos para transições corretas; sirene/alerta contínuo em caso de bip duplicado ou código não encontrado.

---

## 📁 Estrutura de Arquivos e Funções

| Arquivo | Para que serve? | Como foi usado? |
| :--- | :--- | :--- |
| [`app.py`](./app.py) | **Interface Gráfica do Operador (GUI)** | Construída em Tkinter/TTK com tema escuro e visual industrial; gerencia a lista em tempo real de pedidos, captura eventos do leitor de código de barras e aciona feedback sonoro. |
| [`database.py`](./database.py) | **Camada de Dados & Persistência (SQLite)** | Controla a conexão com o banco de dados, cria automaticamente os esquemas de tabelas, gerencia transações com tratamento de concorrência e atualiza timestamps de separação. |
| [`watcher.py`](./watcher.py) | **Observador e Leitor de Arquivos XML** | Monitora a pasta `xml_entrada/` em segundo plano. Assim que o Bling salva uma nova NF-e, o watcher extrai a chave de acesso, destinatário, itens e insere no banco. |
| [`server.py`](./server.py) | **Servidor de Sincronização Local (FastAPI + WebSockets)** | Sincroniza o estado das notas entre múltiplas estações de conferência em rede local com latência imperceptível (< 50ms). |
| [`requirements.txt`](./requirements.txt) | **Dependências do Sistema** | Dependências para backend e GUI (`fastapi`, `uvicorn`, `watchdog`, `pygame`). |
| [`WORKFLOW_SHOPEE_VALIDADOR_EXPEDICAO.md`](./WORKFLOW_SHOPEE_VALIDADOR_EXPEDICAO.md) | **Procedimento Operacional Padrão (POP)** | Guia operacional completo para treinamento de colaboradores na bancada de expedição. |

---

## 🔒 Conformidade LGPD & Segurança
Por diretrizes estritas de privacidade e proteção de dados (LGPD):
* Arquivos de banco de dados (`pedidos.db`) e XMLs de notas fiscais reais contendo dados pessoais de clientes (nomes, endereços, CPFs) estão devidamente ignorados via `.gitignore`.
* Ao iniciar a aplicação em um ambiente limpo, o sistema cria automaticamente um banco de dados novo e vazio pronto para uso.

---

## 🚀 Como Executar

```bash
# 1. Instalar as dependências
pip install -r requirements.txt

# 2. Iniciar o validador de expedição
python app.py
```
