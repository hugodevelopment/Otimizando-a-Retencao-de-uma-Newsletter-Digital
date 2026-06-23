# Otimizando-a-Reten-o-de-uma-Newsletter-Digital

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

*   **Otimização de Memória:** O dataset de eventos superava 170MB. Realizei uma filtragem cirúrgica de colunas em nível de carregamento, mantendo apenas os campos essenciais de rastreio e economizando memória RAM do pipeline.
*   **Tratamento de Inconsistências Temporais:** Durante a análise exploratória, detectei a presença de índices de período (`period_index`) negativos. Isso indicava eventos gravados pelo sistema antes da data oficial de cadastro do usuário. Apliquei um filtro booleano estrito (`period_index >= 0`) para limpar o ruído e focar puramente no ciclo de vida do cliente pós-onboarding.
*   **Normalização de Séries Temporais:** Utilizei o método `.normalize()` do Pandas para zerar os horários flutuantes dos eventos e reconstruir a coluna de horário fixo de disparo (06h00) via `to_timedelta()`, permitindo o cálculo exato de velocidade de reação do leitor.

---

## 📊 Principais Insights de Growth obtidos no Python

### 1. Matriz de Coorte por Canal de Aquisição
Ao quebrar a análise de Cohort horizontal utilizando a coluna `acquisition_channel`, as métricas revelaram um cenário crítico:
*   Os usuários adquiridos via **'Email'** e **'Organic'** mantêm uma taxa de retenção linear estável acima de 45% até o Mês 3.
*   A safra de usuários vindos de **'Paid Search' (Anúncios Pagos)** apresentou uma queda abrupta (vazamento), registrando um Churn de aproximadamente 70% logo no Mês 1. 

### 2. Análise do Perfil de Leitor (Tempo de Reação)
Considerando a premissa de negócio de que o disparo ocorre rigorosamente entre 06h00 e 06h20, a análise de tempo de resposta mostrou que:
*   Leitores orgânicos concentram-se no perfil **"Café da Manhã"** (abrem em menos de 1 hora após o envio), indicando alto valor de marca e engajamento habitual.
*   Leitores de anúncios pagos concentram-se no perfil **"Retardatário"** (demoram mais de 9 horas para abrir), apresentando maior probabilidade de Churn nos meses seguintes.

---

## 💡 O que fazemos com isso? (Planos de Ação Propostos)

Com base nos dados reais consolidados, as recomendações estratégicas para o time de Produto e Growth são:

1.  **Eficiência de Budget:** Pausar ou reduzir em 15% o orçamento de campanhas focadas em *Paid Search*, redirecionando essa verba para estratégias de SEO e parcerias de co-marketing orgânico, visto que o CAC (Custo de Aquisição) de mídia paga não está se pagando devido ao Churn imediato.
2.  **Onboarding Preditivo:** Criar um gatilho de automação no sistema de e-mails para monitorar o perfil do usuário na primeira semana. Se o assinante recém-chegado for classificado como "Retardatário" nos primeiros 3 dias, disparar uma sequência diferenciada de reengajamento com assuntos (subjects) mais agressivos para tentar quebrar a curva de queda do primeiro mês.

