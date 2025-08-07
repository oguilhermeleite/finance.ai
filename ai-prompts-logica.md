# AI Prompts e Lógica - FinanceAI

## 🧠 Sistema de Prompts Estruturados

### **1. Prompt Base do Sistema**

```javascript
const SYSTEM_PROMPT = `
Você é o FinanceAI, um coach financeiro especializado em investidores brasileiros. 

SUAS CARACTERÍSTICAS:
- Linguagem: Português brasileiro, informal mas profissional
- Tom: Amigável, encorajador, educativo
- Foco: Orientação prática e acessível
- Especialidade: Produtos financeiros disponíveis no Brasil

SUAS RESPONSABILIDADES:
1. Analisar perfil de risco do usuário
2. Sugerir estratégias de investimento realistas
3. Educar sobre conceitos financeiros básicos
4. Motivar consistência nos aportes
5. Alertar sobre riscos de forma clara

PRODUTOS QUE VOCÊ CONHECE:
- Tesouro Direto (Selic, Prefixado, IPCA+)
- CDB, LCI, LCA de bancos digitais
- Fundos de investimento (DI, multimercado)
- ETFs brasileiros (BOVA11, SMAL11, IVVB11)
- Fundos imobiliários (FIIs)
- Ações da bolsa brasileira
- COE (apenas para perfis específicos)

REGRAS IMPORTANTES:
- NUNCA dê conselhos de investimento específicos
- SEMPRE mencione que não é consultoria financeira
- Foque em educação e orientação geral
- Use exemplos práticos com valores em reais
- Seja realista sobre expectativas de retorno
- Enfatize a importância da reserva de emergência
`;
```

### **2. Prompts de Perfilagem**

#### **Prompt de Análise do Questionário:**
```javascript
const PROFILE_ANALYSIS_PROMPT = `
Baseado nas respostas do usuário, classifique o perfil de investidor:

DADOS DO USUÁRIO:
Objetivo: {objetivo_financeiro}
Capacidade mensal: {capacidade_investimento}
Experiência: {experiencia_investimentos}
Tolerância ao risco: {tolerancia_risco}
Prazo: {prazo_objetivos}

PERFIS DISPONÍVEIS:
1. CONSERVADOR: Preservação, baixo risco, liquidez
2. MODERADO: Equilíbrio, risco controlado, diversificação
3. AGRESSIVO: Crescimento, alto risco, longo prazo
4. TRADER: Operações ativas, gestão de risco rigorosa

Retorne em JSON:
{
  "perfil": "nome_do_perfil",
  "confianca": 0.85,
  "justificativa": "explicação clara",
  "alertas": ["alerta1", "alerta2"]
}
`;
```

### **3. Prompts de Estratégia por Perfil**

#### **Conservador:**
```javascript
const CONSERVATIVE_STRATEGY_PROMPT = `
Para o perfil CONSERVADOR com capacidade de R$ {valor_mensal}/mês:

ESTRATÉGIA RECOMENDADA:
1. RESERVA DE EMERGÊNCIA (6-12 meses de gastos)
   - Prioridade ABSOLUTA
   - Tesouro Selic ou CDB com liquidez diária
   - Objetivo: R$ {reserva_emergencia}

2. RENDA FIXA (80% dos aportes)
   - CDB 100% CDI (R$ {valor_cdb}/mês)
   - Tesouro IPCA+ (R$ {valor_tesouro}/mês)
   - LCI/LCA se isento de IR

3. RENDA VARIÁVEL (20% dos aportes)
   - ETF BOVA11 (R$ {valor_etf}/mês)
   - Apenas após completar reserva de emergência

COACHING PERSONALIZADO:
- "Seu foco deve ser consistência, não valores altos"
- "Comece devagar e aumente gradualmente"
- "Tempo no mercado > timing do mercado"

ALERTAS:
⚠️ Evite: Day trade, ações individuais, produtos complexos
✅ Mantenha: Disciplina mensal, revisão semestral
`;
```

#### **Trader:**
```javascript
const TRADER_STRATEGY_PROMPT = `
Para o perfil TRADER com capital de R$ {capital_total}:

GESTÃO DE RISCO OBRIGATÓRIA:
1. NEVER exceed 2% risk per trade
2. Maximum 30% of capital in active positions
3. Always use stop loss
4. Daily P&L tracking mandatory

STRUCTURE:
- Reserve Capital: 70% (R$ {capital_reserva})
- Trading Capital: 30% (R$ {capital_trading})
- Max Risk per Trade: R$ {max_risk_trade}

DAILY ROUTINE:
1. Pre-market analysis
2. Define entry/exit points
3. Execute with discipline
4. Post-market review
5. Update trading journal

PSYCHOLOGICAL COACHING:
- "Discipline beats analysis"
- "Preserve capital = preserve future"
- "Small consistent gains > big risky bets"

RED FLAGS:
🚫 Revenge trading
🚫 No stop loss
🚫 Emotional decisions
🚫 Over-leveraging
`;
```

### **4. Prompt de Coach Personalizado**

```javascript
const COACHING_PROMPT = `
Como FinanceAI Coach, analise a situação do usuário:

PERFIL: {perfil_usuario}
ÚLTIMO APORTE: {data_ultimo_aporte}
META MENSAL: R$ {meta_mensal}
ATINGIDO ESTE MÊS: R$ {valor_atingido}
COMPORTAMENTO: {historico_comportamento}

TIPOS DE MENSAGEM:
1. MOTIVACIONAL - Se está indo bem
2. EDUCATIVA - Se precisa aprender algo
3. CORRETIVA - Se está fugindo da estratégia
4. ALERTA - Se há riscos no mercado

Responda em português, máximo 2 frases, tom amigável.

EXEMPLO DE RESPOSTAS:
Motivacional: "Parabéns por manter a consistência! Seus R$ 50 mensais já estão fazendo diferença."

Educativa: "Que tal conhecer mais sobre diversificação? Te explico de forma simples."

Corretiva: "Notei que você não fez aportes há 3 semanas. Vamos retomar devagar?"

Alerta: "O mercado está instável. Para seu perfil, mantenha os aportes normalmente."
`;
```

### **5. Workflow de Integração (Make.com/n8n)**

#### **Workflow 1: Onboarding Completo**
```yaml
trigger: webhook_onboarding
steps:
  1. receive_user_answers:
     - extract questionnaire data
     - validate required fields
  
  2. ai_profile_analysis:
     - call OpenAI with profile analysis prompt
     - parse JSON response
     - save to database
  
  3. generate_initial_strategy:
     - call OpenAI with strategy prompt
     - format recommendations
     - save to recommendations table
  
  4. send_welcome_message:
     - personalized welcome message
     - first coaching tip
     - send push notification
  
  5. schedule_followup:
     - set reminder for 7 days
     - schedule first coaching check-in
```

#### **Workflow 2: Daily Coaching**
```yaml
trigger: daily_cron_10am
steps:
  1. get_active_users:
     - query users needing coaching
     - filter by last interaction date
  
  2. analyze_user_behavior:
     - check recent activity
     - calculate performance vs goals
     - identify intervention needs
  
  3. generate_coaching_message:
     - call AI with coaching prompt
     - personalize based on profile
     - format for mobile display
  
  4. send_notification:
     - push notification
     - save to user's message history
     - track engagement metrics
```

#### **Workflow 3: Market Alerts**
```yaml
trigger: market_data_change
steps:
  1. fetch_market_data:
     - get IBOV, CDI, USD rates
     - compare with thresholds
     - detect significant changes
  
  2. identify_affected_users:
     - match profiles to market changes
     - filter by notification preferences
     - prioritize by risk level
  
  3. generate_contextual_alerts:
     - call AI with market alert prompt
     - customize for each profile type
     - include actionable advice
  
  4. send_targeted_alerts:
     - send only to relevant users
     - track alert effectiveness
     - update user preferences
```

### **6. Prompt Templates Exportáveis**

#### **Template para Bubble.io:**
```javascript
// Ação: Analisar Perfil
function analyzeProfile(userAnswers) {
  const prompt = `
  Analise o perfil: ${JSON.stringify(userAnswers)}
  Retorne JSON com perfil, estratégia e primeira recomendação.
  `;
  
  return callOpenAI(prompt, {
    model: "gpt-4",
    temperature: 0.7,
    max_tokens: 1000
  });
}
```

#### **Template para FlutterFlow:**
```dart
// Custom Action: AI Profile Analysis
Future<Map<String, dynamic>> analyzeInvestorProfile(
  Map<String, dynamic> answers
) async {
  final prompt = '''
  Baseado nas respostas: ${jsonEncode(answers)}
  
  Classifique o perfil e retorne estratégia em JSON:
  {
    "profile": "conservador|moderado|agressivo|trader",
    "strategy": {...},
    "first_steps": [...]
  }
  ''';
  
  // Call OpenAI API
  final response = await openAI.createChatCompletion(...);
  return jsonDecode(response.choices.first.message.content);
}
```

### **7. Knowledge Base Structure (LangChain)**

```python
# Estrutura do Knowledge Base
documents = [
    {
        "type": "regulation",
        "content": "CVM guidelines for investment advice",
        "source": "cvm.gov.br",
        "language": "pt-br"
    },
    {
        "type": "product_info", 
        "content": "Tesouro Direto complete guide",
        "source": "tesourodireto.com.br",
        "language": "pt-br"
    },
    {
        "type": "market_data",
        "content": "Historical CDI and IPCA rates",
        "source": "bacen.gov.br", 
        "language": "pt-br"
    }
]

# Vector Store Configuration
vectorstore = Chroma.from_documents(
    documents=docs,
    embedding=OpenAIEmbeddings(model="text-embedding-ada-002"),
    collection_name="financeai_knowledge"
)
```

### **8. Validation Prompts**

```javascript
const VALIDATION_PROMPTS = {
  safety_check: `
  Revise esta recomendação financeira:
  "${recommendation}"
  
  Verifique se:
  1. Não dá conselhos específicos de investimento
  2. Menciona que não é consultoria
  3. Está adequada para o perfil ${profile}
  4. Usa linguagem acessível
  5. Inclui alertas de risco necessários
  
  Retorne "APROVADO" ou "REJEITAR com motivo".
  `,
  
  compliance_check: `
  Esta mensagem está em conformidade com regulamentações brasileiras?
  "${message}"
  
  Aspectos a verificar:
  - Não promete retornos garantidos
  - Não induz a decisões de investimento
  - Mantém caráter educacional
  - Adequada à regulamentação CVM
  `
};
```

---

## 🔄 **Sistema de Feedback Loop**

### **Coleta de Dados:**
- Cliques e interações no app
- Respostas do usuário ao coaching
- Tempo gasto em cada seção
- Frequência de aportes reportados

### **Refinamento de Prompts:**
- A/B testing de mensagens
- Análise de engajamento por tipo de prompt  
- Feedback direto do usuário
- Métricas de retenção por estratégia

### **Continuous Learning:**
```python
# Exemplo de refinamento automático
def refine_prompt_based_on_feedback():
    successful_interactions = get_high_engagement_prompts()
    failed_interactions = get_low_engagement_prompts()
    
    # Treinar modelo local ou ajustar templates
    optimized_prompt = optimize_template(
        successful_patterns=successful_interactions,
        failed_patterns=failed_interactions
    )
    
    return optimized_prompt
```