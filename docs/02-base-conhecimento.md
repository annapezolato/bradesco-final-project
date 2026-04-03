# Base de Conhecimento

> [!TIP]
> **Prompt usado para esta etapa:**
> 
> Organize a base de conhecimento do agente "Lia" usando os arquivos da pasta data/. Explique para que serve cada arquivo e como eles ajudam o agente a identificar padrões de comportamento financeiro do usuário. Monte um exemplo de contexto estruturado que será enviado para o LLM.
>
> [cole ou anexe o template `02-base-conhecimento.md` pra contexto]

## Dados Utilizados

| Arquivo | Formato | Para que serve na Lia? |
|---------|---------|---------------------|
| `historico_interacoes.csv` | CSV | Armazena conversas anteriores para identificar evolução de comportamento e evitar repetição de perguntas. |
| `perfil_usuario.json` | JSON | Define características comportamentais (ex: impulsivo, planejador, evitador) para personalizar as interações. |
| `categorias_gastos.json` | JSON | Classifica tipos de despesas (essencial, supérfluo, impulsivo) para análise de hábitos. |
| `transacoes.csv` | CSV | Base principal para identificar padrões reais de consumo do usuário. |

---

## Adaptações nos Dados

> Você modificou ou expandiu os dados mockados? Descreva aqui.

Remoção completa de dados relacionados a produtos financeiros, isso elimina o risco do agente “escorregar” para recomendação de investimento. Inclusão de categorias comportamentais de gasto, como: gasto por impulso, gasto emocional, gasto recorrente evitável.
Ajuste no perfil do usuário para refletir comportamento, não conhecimento técnico (ex: “sabe o que é orçamento, mas não segue”).

---

## Estratégia de Integração

### Como os dados são carregados?
> Descreva como seu agente acessa a base de conhecimento.

Existem duas possibilidades, injetar os dados diretamente no prompt (Ctrl + C, Ctrl + V) ou carregar os arquivos via código, como no exemplo abaixo:

```python
import pandas as pd
import json

# Carregar perfil comportamental do usuário
perfil = json.load(open('./data/perfil_usuario.json', encoding='utf-8'))
# Carregar transações financeiras
transacoes = pd.read_csv('./data/transacoes.csv')
# Carregar histórico de interações com o agente
historico = pd.read_csv('./data/historico_interacoes.csv')
# Carregar categorias de gastos (classificação comportamental)
categorias = json.load(open('./data/categorias_gastos.json', encoding='utf-8'))
```

### Como os dados são usados no prompt?
> Os dados vão no system prompt? São consultados dinamicamente?

Para simplificar, podemos simplesmente "injetar" os dados em nosso prompt, agarntindo que o Agente tenha o melhor contexto possível. Lembrando que, em soluções mais robustas, o ideal é que essas informaçoes sejam carregadas dinamicamente para que possamos ganhar flexibilidade.

```text
DADOS DO CLIENTE E PERFIL (data/perfil_investidor.json):
{
  "nome": "João Silva",
  "idade": 32,
  "profissao": "Analista de Sistemas",
  "renda_mensal": 5000.00,
  "nivel_consciencia_financeira": "medio",
  "tipo_comportamento": "impulsivo",
  "objetivo_principal": "Reduzir gastos desnecessarios e melhorar controle financeiro",
  "principais_dificuldades": [
    "gastos frequentes com delivery",
    "falta de planejamento mensal",
    "compras por impulso"
  ],
  "gatilhos_comportamentais": [
    "cansaço",
    "praticidade",
    "estresse"
  ],
  "metas": [
    {
      "meta": "Reduzir gastos com delivery",
      "descricao": "Diminuir pedidos de comida durante a semana",
      "prazo": "2026-06"
    },
    {
      "meta": "Organizar gastos mensais",
      "descricao": "Acompanhar e revisar despesas semanalmente",
      "prazo": "2026-05"
    }
  ]
}

TRANSACOES DO CLIENTE (data/transacoes.csv):
data,descricao,categoria,valor,tipo
2026-03-01,Salário,receita,5000.00,entrada
2026-03-02,Aluguel,moradia,1200.00,essencial
2026-03-03,Supermercado,alimentacao,420.00,essencial
2026-03-04,Netflix,recorrente,55.90,recorrente
2026-03-05,Delivery iFood,delivery,78.00,impulsivo
2026-03-06,Uber,transporte,35.00,essencial
2026-03-07,Delivery iFood,delivery,65.00,impulsivo
2026-03-10,Restaurante,alimentacao,120.00,impulsivo
2026-03-12,Academia,recorrente,99.00,recorrente
2026-03-15,Delivery iFood,delivery,82.00,emocional
2026-03-18,Supermercado,alimentacao,210.00,essencial
2026-03-20,Compras online,compras_online,180.00,impulsivo
2026-03-22,Delivery iFood,delivery,70.00,impulsivo
2026-03-25,Streaming extra,recorrente,19.90,evitavel

HISTORICO DE ATENDIMENTO DO CLIENTE (data/historico_atendimento.csv):
data,tema,resumo,insight
2026-03-05,Gastos impulsivos,Usuário relatou excesso de pedidos de delivery,Possível padrão ligado à praticidade
2026-03-10,Planejamento,Usuário disse que não acompanha gastos,Baixa disciplina financeira
2026-03-15,Comportamento emocional,Relatou gastar mais quando está cansado,Gatilho emocional identificado
2026-03-20,Tentativa de mudança,Tentou reduzir pedidos mas não conseguiu,Barreira comportamental
2026-03-25,Reflexão,Reconheceu padrão de impulso,Fase inicial de consciência

---

## Exemplo de Contexto Montado

> Mostre um exemplo de como os dados são formatados para o agente.

O exemplo de contexto montado abaixo, se baiseia nos dados originais da base de conhecimento, mas os sintetiza deixando apenas as informações mais relevantes, otimizando assim o consumo de tokens. Entretanto, vale lembrar que mais importante do que economizar tokens, é ter todas as informações relevantes disponíveis em seu contexto.

```
DADOS DO CLIENTE:
- Nome: João Silva
- Perfil: Moderado
- Objetivo: Construir reserva de emergência
- Reserva atual: R$ 10.000 (meta: R$ 15.000)

RESUMO DE GASTOS:
- Moradia: R$ 1.380
- Alimentação: R$ 570
- Transporte: R$ 295
- Saúde: R$ 188
- Lazer: R$ 55,90
- Total de saídas: R$ 2.488,90

```
