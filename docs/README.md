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
| **Em Separação** | 🟡 **Amarelo** | DANFE bipado a 1ª vez | Pedido em separação no estoque (Cooldown de 300s p/ 2º bip) |
| **Pronto para Coleta** | 🟢 **Verde** | DANFE bipado a 2ª vez | Pedido conferido, separado e pronto p/ despacho |

*Alerta Sonoro:* Bips curtos e positivos para transições corretas; sirene/alerta contínuo em caso de bip duplicado, cooldown ou código não encontrado.

---

## 📁 Arquitetura em Camadas (Padrão MVC / DAO)

| Arquivo / Pacote | Para que serve? | Como foi usado? |
| :--- | :--- | :--- |
| [`src/Main.py`](../src/Main.py) | **Ponto de Entrada (`main`)** | Inicializa os pacotes da aplicação e abre a janela principal do sistema. |
| [`src/view/TelaPrincipal.py`](../src/view/TelaPrincipal.py) | **Interface Gráfica do Operador (View)** | Construída em Tkinter/TTK com tema escuro industrial; gerencia a lista em tempo real de pedidos, captura eventos do leitor de código de barras e aciona feedback sonoro. |
| [`src/dao/PedidoDAO.py`](../src/dao/PedidoDAO.py) | **Camada de Dados & Persistência (DAO / SQLite)** | Controla a conexão com o banco de dados, cria automaticamente os esquemas de tabelas, valida o cooldown de 300s entre bips e exporta relatórios CSV. |
| [`src/service/XmlService.py`](../src/service/XmlService.py) | **Serviço Observador e Leitor de XML (Service)** | Monitora a pasta `xml_entrada/`. Extrai a chave de acesso de 44 dígitos e número da NF-e e insere no banco. |
| [`src/controller/RedeController.py`](../src/controller/RedeController.py) | **Controlador de Sincronização em Rede (Controller)** | Servidor FastAPI + WebSockets que sincroniza o estado das notas entre múltiplas estações de conferência em rede local (< 50ms). |
| [`requirements.txt`](../requirements.txt) | **Dependências do Sistema** | Dependências para backend e GUI (`fastapi`, `uvicorn`, `watchdog`, `websockets`). |
| [`docs/WORKFLOW_SHOPEE_VALIDADOR_EXPEDICAO.md`](./WORKFLOW_SHOPEE_VALIDADOR_EXPEDICAO.md) | **Procedimento Operacional Padrão (POP)** | Guia operacional completo para treinamento e manutenção. |

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
python src/Main.py
```
