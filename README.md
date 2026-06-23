# 📬 Analytics de Produto & Growth: Otimizando a Retenção de uma Newsletter Digital

## 🎯 O Objetivo do Projeto
Este projeto foi desenvolvido com foco em inteligência de produto e estratégias de Growth para modelos de negócios baseados em assinaturas e mídia digital (como o *The News*). O objetivo principal não é apenas monitorar o volume de acessos, mas sim aplicar **raciocínio orientado a hipóteses** para mapear o comportamento de leitura pós-cadastro, identificar gargalos de engajamento e combater o Churn de forma preditiva.

---

## 🔬 A Dor do Negócio & Hipótese Investigada
**Pergunta de Negócio:** *"O volume de novos assinantes se traduz em leitores fiéis no longo prazo, ou estamos sofrendo com um vazamento silencioso na base (Churn) enquanto os canais orgânicos e de e-mail trazem mais valor real?"*

Para responder a isso, estruturei uma análise robusta dividida em duas grandes frentes:
1. **Coorte Segmentada por Canal (Behavioral Cohort):** Acompanhar a sobrevida dos usuários cruzando a safra de entrada com a origem do cliente.
2. **Tempo de Reação (Time-to-Open):** Mensurar o nível de prioridade da newsletter na rotina do usuário, calculando quanto tempo ele demora para interagir após o disparo oficial das 06h00.

---

## 🛠️ Arquitetura do Banco de Dados & Relações
A análise foi realizada integrando dois datasets principais com uma relação de cardinalidade de **1 para Muitos (1:N)**:
*   `customers.csv` (Tabela de Dimensão): Dados cadastrais e canal de aquisição do cliente (`customer_id`, `signup_date`, `acquisition_channel`).

<img width="498" height="281" alt="image" src="https://github.com/user-attachments/assets/92830be6-4435-46f4-ada8-fba5300e2714" />

  
*   `events.csv` (Tabela de Fato - 175 MB): Histórico massivo de interações de leitura em tempo real (`event_id`, `timestamp`, `customer_id`, `event_type`).


<img width="593" height="279" alt="image" src="https://github.com/user-attachments/assets/50c313f4-d3df-4ae3-bc06-d832537f52d0" />


---

## 🧽 Engenharia de Dados & Tratamento de "Dado Sujo"
Antes de gerar qualquer indicador de negócio, apliquei técnicas rigorosas de manipulação de dados com o **Pandas** para garantir a integridade da análise:

*   **Otimização de Memória:** O dataset de eventos superava 170MB. Realizei uma filtragem de colunas em nível de carregamento, mantendo apenas os campos essenciais visando a economia memória RAM do pipeline.
*   **Tratamento de Inconsistências Temporais:** Durante a análise exploratória, detectei a presença de índices de período (`period_index`) negativos. Isso indicava eventos gravados pelo sistema antes da data oficial de cadastro do usuário. Apliquei um filtro booleano estrito (`period_index >= 0`) para limpar o ruído e focar puramente no ciclo de vida do cliente pós-onboarding.
---

## 📊 Principais Insights de Growth obtidos no Python

### 1. Matriz de Coorte por Canal de Aquisição
Ao quebrar a análise de Cohort horizontal utilizando a coluna `acquisition_channel` de e-mail, as métricas revelaram um cenário crítico:

<img width="829" height="571" alt="image" src="https://github.com/user-attachments/assets/320d810d-2f88-43d7-b756-d850449fd402" />


* Resiliência a Longo Prazo (Safra 2023-01): "A coorte de Janeiro (2023-01) mostra um engajamento fantástico. Mesmo após 7 meses de cadastro (período 7), a retenção volta a atingir 100%. Isso prova que o usuário adquirido via e-mail cria um hábito de leitura sólido e consistente de longo prazo, mantendo o produto vivo na rotina dele.

* "A "Morte" Pós-Período 3 (Safras Recentes): "Nota-se um padrão claro de quebra nas safras de Abril (2023-04) e Maio (2023-05). Elas mantêm um engajamento perfeito até o período 3 e depois morrem completamente para 0%."

### 2. Análise do Perfil de Leitor (Tempo de Reação)

<img width="989" height="590" alt="image" src="https://github.com/user-attachments/assets/2684d9ca-a8f7-4048-92d4-cbc2cb63af7d" />



Considerando a premissa de negócio de que o disparo ocorre rigorosamente entre 06h00 e 06h20, a análise de tempo de resposta mostrou que:

O comportamento do leitor é majoritariamente tardio. A barra mais longa é justamente a do perfil "4. Retardatário (Mais de 9h)", que concentra 42.4% de toda a sua base de interações.

O Cenário: Se somarmos o perfil "3. Almoço" com o "4. Retardatário", temos 69% dos leitores abrindo a newsletter muitas horas após o disparo das 06h00.Apenas 12.8% das pessoas leem o e-mail no "Café da Manhã" (na primeira hora).O Risco de Negócio: Para uma newsletter de notícias diárias, abrir o e-mail mais de 9 horas depois (ou seja, após as 15h00) significa que o conteúdo já está "frio". 

O leitor provavelmente já viu as principais notícias do dia em portais ou redes sociais, o que reduz drasticamente a relevância percebida do produto e aumenta o risco de Churn no longo prazo.

---

## 💡 O que fazemos com isso? (Planos de Ação Propostos)

Com base nos dados reais consolidados, as recomendações estratégicas para o time de Produto e Growth são:

1.  **Eficiência de Budget:** Pausar ou reduzir em 15% o orçamento de campanhas focadas em *Paid Search*, redirecionando essa verba para estratégias de SEO e parcerias de co-marketing orgânico, visto que o CAC (Custo de Aquisição) de mídia paga não está se pagando devido ao Churn imediato.
2.  **Onboarding Preditivo:** Criar um gatilho de automação no sistema de e-mails para monitorar o perfil do usuário na primeira semana. Se o assinante recém-chegado for classificado como "Retardatário" nos primeiros 3 dias, disparar uma sequência diferenciada de reengajamento com assuntos (subjects) mais agressivos para tentar quebrar a curva de queda do primeiro mês.

