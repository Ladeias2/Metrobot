# MetrôBot SP

Projeto acadêmico de Inteligência Artificial e Machine Learning que combina busca em grafos, lógica simbólica, modelos de linguagem e interface interativa para calcular rotas em uma representação simplificada do Metrô de São Paulo.

> **Regra de arquitetura:** o LLM conversa, o algoritmo decide.

## Integrantes

- Ana Carolina Sampaio dos Santos
- Marcus Vinicius Ladeia Correa
- Vitoria Pereira Cardoso da Silva

## Objetivos

O projeto implementa:

- Modelagem das Linhas 1-Azul, 2-Verde e 3-Vermelha como grafo
- 52 estações únicas
- Busca em largura, BFS
- Busca em profundidade, DFS
- Comparação do esforço das buscas
- Estações fechadas e linhas paralisadas
- Identificação de baldeações
- Lógica proposicional e tabela-verdade
- Lógica de primeira ordem
- Encadeamento para frente
- Justificativas das inferências
- Intérprete e narrador com Llama
- Integração opcional com Groq ou Ollama
- Modo offline sem dependência de API
- Interface com `ipywidgets`
- Testes automatizados

## Funcionalidades adicionais

O notebook também contém itens opcionais para pontuação adicional:

- Busca que prioriza menos baldeações
- Dijkstra para rota de menor tempo estimado
- Penalidade de 5 minutos por baldeação
- Simulação de paralisação de linha por regra lógica
- Mapa da rede com NetworkX e Matplotlib
- Relatório gráfico de esforço BFS versus DFS

## Estrutura do repositório

```text
.
├── MetroBot_SP_2_0_Nota_Maxima_Corrigido.ipynb
├── README.md
└── .gitignore
```

## Requisitos

- Python 3.10 ou superior
- Jupyter Notebook, JupyterLab, Google Colab ou VS Code com extensão Jupyter

Principais bibliotecas:

```text
ipywidgets
matplotlib
networkx
python-dotenv
groq
ollama
```

O próprio notebook contém uma célula que verifica e instala as dependências opcionais necessárias.

## Como executar no Google Colab

1. Faça upload do arquivo `MetroBot_SP_2_0_Nota_Maxima_Corrigido.ipynb`.
2. Abra o notebook.
3. Selecione **Ambiente de execução → Executar tudo**.
4. Aguarde a instalação das dependências.
5. Verifique se a célula de testes apresenta a mensagem de sucesso.
6. Use o painel interativo exibido ao final.

## Como executar no VS Code

Crie e ative um ambiente virtual:

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
3. Execute todas as células em ordem.

## Modos de execução do LLM

No notebook, altere a variável:

```python
PROVEDOR = "offline"
```

Opções disponíveis:

- `offline`: não utiliza LLM nem internet
- `groq`: utiliza uma API hospedada
- `ollama`: utiliza um modelo local

O modo padrão é `offline`, garantindo que a aplicação e os testes funcionem sem chave de API.

## Configuração do Groq

Nunca coloque a chave diretamente no notebook.

### Arquivo `.env`

Crie um arquivo `.env` na raiz do projeto:

```env
GROQ_API_KEY=sua_chave_aqui
GROQ_MODEL=nome_do_modelo_disponivel
```

O arquivo `.env` está incluído no `.gitignore` e não deve ser enviado ao GitHub.

No Google Colab, utilize os Secrets e crie a variável `GROQ_API_KEY`.

## Configuração do Ollama

Após instalar o Ollama, baixe o modelo:

```bash
ollama pull llama3.2
```

Depois, altere no notebook:

```python
PROVEDOR = "ollama"
```

## Arquitetura

```text
Pedido em linguagem natural
           ↓
LLM ou interpretador offline
           ↓
Validação dos nomes
           ↓
Base de conhecimento e inferência
           ↓
BFS, DFS ou busca bônus
           ↓
Cálculo de paradas e baldeações
           ↓
Narrador e interface
```

O LLM não escolhe a rota. Ele é usado somente para interpretar o pedido e explicar um resultado já calculado por algoritmos determinísticos.

## Regras lógicas

O projeto utiliza sete regras principais:

1. Um local próximo de uma estação determina a origem.
2. Um local próximo de uma estação determina o destino.
3. Uma estação fechada torna-se bloqueada.
4. Uma estação com elevador em manutenção pode ficar inacessível.
5. Origem ou destino inacessível gera alerta.
6. Uma estação pertencente a mais de uma linha é uma integração.
7. Uma linha paralisada torna seus trechos indisponíveis.

As integrações Sé, Paraíso e Ana Rosa são deduzidas pelo motor de inferência, e não cadastradas diretamente.

## Testes

Execute no notebook:

```python
rodar_testes()
```

Os testes verificam, entre outros pontos:

- Quantidade de estações no grafo
- Trecho compartilhado entre Paraíso e Ana Rosa
- Integrações deduzidas
- Rotas e baldeações obrigatórias
- Estações fechadas
- Alertas de acessibilidade
- Paralisação de linha
- Validação de nomes
- Modo offline
- Dijkstra e busca com menos baldeações

## Explicação dos casos com Paraíso fechada

Quando Paraíso está fechada, a Linha 2 fica interrompida entre Brigadeiro e Ana Rosa.

- De Vila Madalena para Jabaquara, não existe rota, pois o lado oeste da Linha 2 perde acesso à Linha 1.
- De Vila Prudente para Jabaquara, ainda existe rota via Ana Rosa, onde é possível acessar a Linha 1.

## Segurança

- Não publique chaves de API.
- Não remova `.env` do `.gitignore`.
- Revise o histórico do Git antes de publicar.
- Caso uma chave tenha sido exposta, revogue-a imediatamente e gere outra.

## Uso de inteligência artificial

Este projeto utilizou assistência de IA para apoiar a estruturação do notebook, a revisão do código e a criação de testes. As rotas são decididas por algoritmos determinísticos, e as saídas do modelo de linguagem passam por validação. O grupo revisou o funcionamento e deve estar preparado para explicar as decisões de implementação.

## Observação acadêmica

Este repositório tem finalidade educacional. Os dados representam um modelo simplificado de três linhas e não devem ser utilizados como fonte de informações operacionais em tempo real.
