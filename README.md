# MetrôBot SP

Projeto acadêmico de Inteligência Artificial e Machine Learning que aplica busca em grafos, lógica simbólica e processamento de linguagem natural a uma representação didática das Linhas 1-Azul, 2-Verde e 3-Vermelha do Metrô de São Paulo.

> **Princípio do projeto:** o LLM conversa, o algoritmo decide.

## Integrantes

- Ana Carolina Sampaio dos Santos
- Marcus Vinicius Ladeia Correa
- Vitoria Pereira Cardoso da Silva

## Visão geral

O MetrôBot recebe uma origem e um destino, transforma locais conhecidos em estações, aplica regras lógicas e calcula uma rota no grafo. A escolha da rota é realizada por algoritmos determinísticos.

Quando habilitado, o modelo de linguagem é utilizado somente para interpretar pedidos em linguagem natural. A narração atual da rota é produzida pelo modo offline, a partir dos resultados previamente calculados.

O projeto funciona por padrão no modo `offline`, sem chave de API e sem conexão com a internet.

## Funcionalidades obrigatórias

- Modelagem das três linhas como um único grafo
- 52 estações únicas, sem vizinhos duplicados
- Registro da linha ou das linhas disponíveis em cada trecho
- BFS, busca em largura
- DFS, busca em profundidade
- Respeito a estações bloqueadas
- Comparação do número de estações visitadas
- Identificação de paradas e baldeações
- Base com pelo menos três pontos de interesse por linha
- Lógica proposicional e tabela-verdade
- Lógica de primeira ordem
- Encadeamento para frente com justificativas
- Regras R1 a R7
- Inferência automática das integrações Sé, Paraíso e Ana Rosa
- Interpretação de pedidos com validação de nomes
- Funcionamento offline
- Interface com `ipywidgets`
- Visualização das três linhas
- Testes automatizados, incluindo os seis casos obrigatórios

## Funcionalidades adicionais

- Busca que prioriza menos baldeações
- Dijkstra com custo de 2 minutos por trecho e 5 minutos por baldeação
- Simulação de paralisação de linha
- Visualização opcional com NetworkX e Matplotlib
- Relatório comparativo de esforço entre BFS e DFS

## Estrutura do repositório

```text
.
├── MetroBot.ipynb
├── README.md
└── .gitignore
```

## Requisitos

- Python 3.10 ou superior
- Google Colab, Jupyter Notebook, JupyterLab ou VS Code com a extensão Jupyter

Dependências utilizadas:

```text
ipywidgets
matplotlib
networkx
python-dotenv
groq
ollama
```

O notebook possui uma célula que verifica e instala dependências ausentes no mesmo ambiente do kernel. Antes de publicar, confirme que essa célula contém também `groq` e `ollama` caso esses provedores sejam utilizados.

## Execução no Google Colab

1. Faça upload de `MetroBot.ipynb`.
2. Abra o notebook.
3. Selecione **Ambiente de execução > Executar tudo**.
4. Aguarde a instalação das dependências.
5. Confirme que a célula de testes terminou sem erros.
6. Utilize o painel interativo exibido nas últimas células.

## Execução no VS Code

Crie um ambiente virtual.

### Windows

```bash
python -m venv .venv
.venv\Scripts\activate
```

### Linux ou macOS

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Instale as dependências:

```bash
python -m pip install --upgrade pip
python -m pip install jupyter ipywidgets matplotlib networkx python-dotenv groq ollama
```

Depois:

1. Abra o notebook no VS Code.
2. Selecione o kernel do ambiente `.venv`.
3. Execute as células em ordem.

## Provedores de linguagem

A variável abaixo controla o modo de execução:

```python
PROVEDOR = "offline"
```

Valores aceitos:

- `offline`: interpretação local por correspondência de nomes
- `groq`: interpretação por API
- `ollama`: interpretação com modelo local

Se o Groq ou o Ollama falhar durante a interpretação, o código utiliza o interpretador offline como fallback.

### Groq

Nunca escreva a chave diretamente no notebook. Para execução local, crie um arquivo `.env`:

```env
GROQ_API_KEY=sua_chave_aqui
GROQ_MODEL=modelo_disponivel_na_sua_conta
```

O arquivo `.env` deve permanecer no `.gitignore`.

No Google Colab, armazene `GROQ_API_KEY` nos Secrets do notebook.

### Ollama

Após instalar o Ollama, baixe o modelo local:

```bash
ollama pull llama3.2
```

Depois, altere o provedor:

```python
PROVEDOR = "ollama"
```

## Arquitetura

```text
Pedido do passageiro
        ↓
Intérprete LLM ou offline
        ↓
Validação de origem e destino
        ↓
Base de conhecimento e inferência
        ↓
BFS, DFS ou busca adicional
        ↓
Paradas, baldeações e tempo estimado
        ↓
Narração offline e interface
```

A rota não é criada pelo LLM. Ela é calculada sobre o grafo, considerando estações bloqueadas e linhas paralisadas.

## Regras lógicas

- **R1:** um local próximo de uma estação determina a origem.
- **R2:** um local próximo de uma estação determina o destino.
- **R3:** uma estação fechada torna-se bloqueada.
- **R4:** uma estação com elevador em manutenção torna-se inacessível quando o usuário precisa de acessibilidade.
- **R5:** origem ou destino inacessível produz um alerta.
- **R6:** uma estação pertencente a mais de uma linha é uma integração.
- **R7:** uma linha paralisada permite deduzir os trechos indisponíveis dessa linha.

As integrações não são cadastradas manualmente. O motor de inferência deduz Sé, Paraíso e Ana Rosa a partir dos fatos `pertence(estação, linha)`.

A busca recebe diretamente o conjunto de linhas paralisadas e desconsidera os trechos atendidos exclusivamente por essas linhas. A R7 registra os fatos `trecho_indisponivel` para inferência e auditoria.

## Testes

Execute:

```python
rodar_testes()
```

Os testes cobrem:

- Quantidade de estações
- Trecho compartilhado Paraíso–Ana Rosa
- Integrações deduzidas
- Rotas e baldeações esperadas
- Estações fechadas
- Alertas de acessibilidade
- Paralisação de linha
- Validação de nomes
- Interpretação offline
- Busca com menos baldeações
- Dijkstra

Ao final, o notebook informa apenas que todos os testes passaram. A quantidade não é usada como critério documental, pois um mesmo `assert` pode validar mais de uma condição.

## Casos com Paraíso fechada

O fechamento de Paraíso impede sua utilização como estação e interrompe as conexões que passam pelo nó.

- **Vila Madalena → Jabaquara:** não há rota no modelo, pois o trecho oeste da Linha 2 perde acesso à Linha 1.
- **Vila Prudente → Jabaquara:** há rota via Ana Rosa, porque essa integração ainda pode ser alcançada pelo trecho leste da Linha 2.

## Limitações

- A rede é uma simplificação acadêmica com apenas três linhas.
- Tempos, bloqueios e manutenções são simulados.
- Os pontos de interesse fazem parte de uma base didática.
- O sistema não consulta dados operacionais em tempo real.
- O narrador atual utiliza o modo offline mesmo quando Groq ou Ollama está habilitado.
- A R7 produz fatos para auditoria, enquanto a busca aplica diretamente o conjunto de linhas paralisadas.

## Segurança

- Não publique chaves de API.
- Mantenha `.env` no `.gitignore`.
- Revise as células e os outputs do notebook antes do commit.
- Procure por textos como `gsk_`, `GROQ_API_KEY=` e outras credenciais antes de publicar.
- Se uma chave tiver sido exposta, revogue-a e gere uma nova.

## Uso de inteligência artificial

Foi utilizada assistência de IA na estruturação, revisão e elaboração de testes. As rotas são calculadas por algoritmos determinísticos e as entradas produzidas pelo modelo passam por validação.


## Finalidade

Projeto destinado exclusivamente a fins acadêmicos e educacionais. Não utilize este sistema como fonte de informações operacionais sobre a rede de transporte.
