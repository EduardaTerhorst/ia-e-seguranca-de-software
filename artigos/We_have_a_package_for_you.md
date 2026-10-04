# Relatório Analítico – “We Have a Package for You!” 

## Resumo Executivo  
Spracklen *et al.* (2025) investigam um novo tipo de risco em **cadeia de suprimentos de software**: as *“alucinações de pacotes”* em código gerado por LLMs de programação. Eles definem **package hallucination** como a situação em que o modelo recomenda ou importa um pacote que **não existe** nos repositórios públicos (PyPI, npm). Esse fenômeno cria um vetor de ataque: atacantes podem publicar um pacote malicioso com exatamente aquele nome fictício, explorando recomendações futuras dos modelos. O estudo é extenso: analisaram **16 LLMs** (comerciais e open-source) em Python e JavaScript, usando **cerca de 19,500 prompts** reais (StackOverflow e gerados por prompts) e geraram **576 mil códigos** no total. A métrica principal é a *taxa de alucinação de pacotes*, definida como o número de pacotes alucinados dividido pelo total de pacotes recomendados. 

Os resultados principais mostram que, no conjunto experimental, **19,7 %** de todos os pacotes recomendados eram fictícios (≈440 mil pacotes), correspondendo a **205.474 nomes únicos** inventados. Houve grande diferença entre modelos: LLMs comerciais geraram ~5,2 % de pacotes alucinados em média, contra ~21,7 % para modelos de código aberto. O GPT-4 Turbo foi o modelo mais conservador (3,59 % de alucinações), enquanto o DeepSeek 1B foi o melhor open-source (13,63 %). Perguntas em **JavaScript** geraram mais alucinações (21,3 %) que em Python (15,8 %). A variação do **parâmetro de temperatura** mostrou que temperaturas altas aumentam muito a taxa de alucinação (GPT-4: 8,9 % em T=2; GPT-3.5: 31,8 % em T=2). Ajustar técnicas de *decoding* (top-p, top-k, min-p) teve efeito marginal (~+1,16 % em média). *Prompts* sobre tópicos recentes (após o fim do treinamento) resultaram em ~10 % mais alucinações.  

Em termos de comportamento, as alucinações mostraram **persistência**: em 500 prompts que geraram pacote inventado, ~43 % dos mesmos nomes voltaram em todas 10 repetições, e 58 % apareceram mais de uma vez. Contudo, diferente de erros simples, 81 % dos nomes fictícios surgiram em **um só modelo**. A análise de similaridade mostrou que poucas alucinações eram quase erros de digitação: apenas 13,4 % dos nomes tinham distância de Levenshtein ≤2 de algum pacote real, enquanto quase metade (48,6 %) tinha distância ≥6. Pacotes que existiam mas foram removidos explicaram só **0,17 %** das alucinações. Observou-se também confusão de linguagem: 8,7 % das alucinações Python eram pacotes legítimos de JavaScript. Por fim, testes de **auto-detecção** mostraram que modelos como GPT-4 Turbo, GPT-3.5 e DeepSeek conseguem identificar suas próprias alucinações com alta precisão (≈80–91 % de precisão e recall). 

Os autores testaram três estratégias de mitigação: **RAG** (retrieval-augmentation com pacotes válidos no prompt), **auto-refinamento** (o modelo verifica suas próprias recomendações e regenera se necessário) e **fine-tuning** supervisionado. Em dois modelos avaliados (DeepSeek e CodeLlama), todas reduziram a taxa de alucinação. Em particular, o fine-tuning foi mais eficaz (DeepSeek: de 16,14 % para 2,66 %), mas custou caro à qualidade funcional (HumanEval do DeepSeek caiu de 51,4 % para 25,3 %). O ensemble das três técnicas mostrou a maior redução (DeepSeek 2,40 %; CodeLlama 9,32 %). Os autores concluem que há um trade-off: reduzir alucinações via fine-tuning prejudica a correção do código, enquanto RAG e auto-refinamento são promissores sem prejuízos tão drásticos.  

Em suma, Spracklen *et al.* evidenciam que **as LLMs sistematicamente inventam dependências** em cenários controlados, criando risco real de ataques de “typosquatting by hallucination” na cadeia de suprimentos. A alucinação de pacotes é um fenômeno **persistente, abrangente e específico de cada modelo**. Isso amplia a discussão de segurança sobre IA em programação: não basta olhar só para vulnerabilidades inseridas no código (Pearce *et al.* 2022), mas também para as recomendações de bibliotecas. Desenvolvedores devem checar rigorosamente qualquer pacote sugerido por IA. As implicações práticas incluem o uso de scanners de dependências, reputação de pacotes e restrições (SCA/SBOM) ao adotar código gerado por IA. Futuros trabalhos devem examinar modelos mais novos e desenvolver técnicas de mitigação específicas, além de envolver usuários reais para avaliar o risco efetivo de adoção desses pacotes.  

## Objetivos e Perguntas de Pesquisa  
O artigo tem como objetivo principal avaliar **systematicamente** a ocorrência e o impacto das *package hallucinations* em LLMs de código. Os autores definem um cenário de adversário que explora recomendações falsas de pacotes para lançar ataques de supply chain. Com base nisso, organizam cinco perguntas de pesquisa (RQ):

- **RQ1:** Quão prevalentes são as alucinações de pacotes ao gerar código em Python e JavaScript com LLMs (comerciais e open-source)? (Avaliar frequência do fenômeno em diversos modelos e tarefas).
- **RQ2:** Como configurações do modelo (temperatura, técnicas de decoding, recência dos dados) afetam as alucinações? (Investigar influência de parâmetros como temperatura e top-k/p e da data dos prompts).
- **RQ3:** Quais comportamentos de modelo se observam nas alucinações? (Analisar persistência/repetição dentro de um modelo, ocorrência em múltiplos modelos, tendências de quantos pacotes são gerados, e capacidade de auto-detecção pelo modelo).
- **RQ4:** Quais características têm os pacotes alucinados? (Examinar similaridade semântica com pacotes reais, cross-language, influência de pacotes deletados).
- **RQ5:** É possível mitigar as alucinações de pacotes de forma eficaz sem prejudicar a qualidade do código? (Testar estratégias conhecidas de redução de “hallucinations”).

Resumidamente, o estudo busca quantificar e caracterizar as alucinações de pacotes e propor contramedidas.

## Dados e Amostras  
Foram construídos dois conjuntos de *prompts* (em inglês) para cobrir variadas tarefas de programação: 

- **StackOverflow:** extrairam *perguntas reais* do StackOverflow. Selecionaram 240 tags populares relacionadas a Python/JavaScript (ex.: algoritmos, bibliotecas, domínios diversos) e coletaram as 20 perguntas mais votadas por tag. Isso gerou **4.800 prompts em Python** e **4.800 em JavaScript**. Para análise temporal, separaram em perguntas feitas em 2023 (recente) e anteriores a 2023 (all-time), dobrando para ~9.600 cada idioma. Os prompts incluem toda a pergunta (não só um enunciado curto), e os modelos foram instruídos a fornecer código apenas se necessário. 

- **LLM-gerado (por pacotes):** a segunda fonte foi usar a descrição de **5.000 pacotes mais populares** (PyPI/npm). Cada descrição foi fornecida ao LLaMA-2 70B, pedindo para gerar um *prompt* de programação relacionado. Obteram ~4.800 prompts por idioma, novamente divididos em *recentes* vs *all-time* por popularidade do pacote (ranking 2023 vs antes de 2023).

Combinados, resultam em **≈19.500 prompts** (simmetricamente Python/JS). Cada prompt foi acompanhado de uma mensagem de sistema fixa (ver Apêndice B) para orientar o formato da resposta. 

Para cada prompt, gerou-se **código fonte** com cada modelo listado abaixo, ao todo 19.200 amostras de código por modelo (16 testes Python + 14 testes JS) e **576.000 códigos** no total. Esses códigos serviram para extrair nomes de pacotes.

## Modelos Avaliados  
Testaram 16 modelos de geração de código, incluindo:

- **Comerciais (black-box):** *ChatGPT/GPT-4* (GPT-4 padrão, GPT-4 Turbo, GPT-3.5 Turbo). As versões exatas são baseadas em API da OpenAI (com “license: Commercial”). Parâmetros exatos são desconhecidos (“Parameters: Unknown”), mas são os mais avançados disponíveis.

- **Open-source:** Vários LLMs de código. Em particular:
  - **CodeLlama** (7B, 13B, 34B) – modelos base, não fine-tuned.
  - **DeepSeek** (1.3B, 6.7B, 33B) – free/open-source modelos recentes (DeepSeek 1B ~1.3B consideraram).
  - **WizardCoder** (Python 7B, geral 34B).
  - **Magicoder** (6.7B), **Mistral** (7B), **Mixtral** (8×7B), **OpenChat** (7B).
  - Nota: *WizardCoder-Python 7B* e *CodeLlama-Python 33B* (fine-tuned em Python) foram testados **só em Python**. 

Os modelos open-source foram executados localmente com quantização GPTQ para agilizar inferência. Todos receberam os mesmos parâmetros por idioma (ver Apêndice C). Em suma, a avaliação envolveu **14 testes em cada idioma** (um por modelo), mais 2 extras (WizardCoder-Python, CodeLlama-Python apenas em Python). Isso permite comparar família a família e comercial vs OSS.

## Desenho Experimental  
Para cada prompt, os modelos geraram um bloco de código fonte em Python ou JavaScript, de tamanho limitado, sem ir ao vivo a repositórios (offline). As configurações padrão de inferência foram usadas (temperatura 0.7 para geração de código, top-p=0.9, top-k=20). Em seguida, empregaram **três heurísticas** para detectar quais pacotes (dependências) eram recomendados no código:

1. **Instalação explícita:** escaneavam o texto gerado (incluindo resposta prose) procurando `pip install` (Python) ou `npm install` (JS). Muitas respostas incluem instruções de instalação. Encontrar um comando “install” revela diretamente um nome de pacote sugerido.

2. **Pergunta de pacotes (auto):** cada código gerado era re-submetido ao mesmo modelo, pedindo: “Que pacotes são necessários para executar este código?”. Assim simulam um usuário que executa e pergunta ao modelo qual dependência falta. O modelo normalmente lista nomes de pacotes.

3. **Prompt original revisitado:** reutilizavam o *prompt original* (pergunta) e pediam ao modelo para listar pacotes úteis para aquela tarefa. Essa forma busca capturar recomendações que o modelo pode dar fora do contexto do código.

Combinando as três fontes de resposta, obtêm-se um *master list* de pacotes recomendados pelo modelo para cada prompt. Em seguida, cada nome de pacote é checado contra os registros oficiais (PyPI/npm). Se **não existir** nesses repositórios, é classificado como *alucinação*. 

A taxa de alucinação de pacotes (**Package Hallucination Rate**) é então calculada como:
\[
\text{Taxa} = \frac{\text{número de pacotes alucinados}}{\text{número total de pacotes recomendados}}
\]
. Essa métrica é usada em todas as análises quantitativas. Foram gerados **2,23 milhões de pacotes** pelas heurísticas em todos os testes, dos quais 440.445 (19,7 %) eram alucinações. O processo experimental, incluindo esquemas de prompts e mensagens de sistema, está documentado em Apêndices.

## Métricas e Definições  
- **Taxa de Alucinação:** definida acima (pacotes fictícios vs total).  
- **Pacotes Únicos Alucinados:** contagem de nomes distintos de pacotes inexistentes (205.474 no total).  
- **Persistência/Repetição:** após identificar pacotes alucinados, repetiu-se cada prompt 10 vezes para ver se surgem os mesmos nomes. Calculam porcentagem de prompts cujo pacote alucinado se repete 0, 1-9 ou 10 vezes.  
- **Cobertura Inter-modelo:** contagem de quantos modelos distintos geraram cada mesmo nome de pacote alucinado (gráfico do número de modelos que produziram aquele pacote).  
- **Distância de Levenshtein:** para cada pacote alucinado, calcula-se a menor distância de Levenshtein até algum pacote válido, para medir semelhança textual.  
- **Auto-detecção de alucinações:** testou-se a capacidade de um modelo dizer se um dado nome de pacote é real ou não. Selecionou-se pacotes válidos e alucinados e perguntou: “Esse pacote [x] é válido?”. A precisão (ration corr/total) e o recall foram medidos (Tabela 2).  
- **Métricas de qualidade de código:** ao avaliar a mitigação por fine-tuning, mediram a precisão do código usando *pass@1* do benchmark HumanEval (compilação + testes) antes/depois do ajuste.

## Resultados Quantitativos  

- **Amostras totais:** foram produzidos 576.000 códigos fonte (cada modelo × cada prompt) e extraídos 2.235.000 pacotes recomendados.
- **Taxa geral de alucinação:** **19,7 %** de todos os pacotes foram fictícios. Isso corresponde a **440.445 pacotes alucinados** e **205.474 nomes únicos**.  
- **Comercial × Open-Source:** A média nos três modelos comerciais (GPT-4 Turbo, GPT-4, GPT-3.5) foi **≈5,2 %**, versus **21,7 %** nos 13 modelos open-source. Ou seja, LLMs comerciais alucinaram **~4× menos**.  
- **Destaques de modelos:** o *GPT-4 Turbo* obteve a menor taxa (3,59 %), o *GPT-4* comum foi 4,05 %. Entre open-source, o melhor foi *DeepSeek 1.3B* com 13,63 %; o pior foi *CodeLlama 7B (geral)* com ~26,28 % (dados completos em Apêndice E).  
- **Línguas:** código Python teve menos alucinações (média 15,8 %) que JavaScript (21,3 %). Note que a tendência por modelo se mantém (modelo “bom” em Python tende a ser bom em JS).  
- **Parâmetros do Modelo:**  
  - *Temperatura:* Em todos os modelos testados, aumentar a temperatura elevou fortemente as alucinações. Por exemplo, no GPT-4 a taxa subiu de <1 % (T<1) para 8,9 % em T=2; no GPT-3.5 chegou a 31,8 % em T=2. Em geral, temperaturas >1 causaram explosão de alucinações.  
  - *Decoding (top-p, top-k, min-p):* Alterar estes parâmetros causou **aumento leve** (média +1,16 % de taxa) em 4 modelos testados. Em outras palavras, filtrar tokens de baixa probabilidade não eliminou o problema: muitos pacotes fictícios surgem mesmo com decodificação “gulosa”.  
  - *Recência dos prompts:* Prompts sobre assuntos recentes (último ano) geraram mais alucinações que prompts sobre o passado. Em média, as perguntas *recentes* apresentaram **10 % mais** alucinações que as *all-time*. Todos os 16 modelos mostraram essa tendência, o que sugere limitação na atualização de conhecimento de LLMs.  

- **Comportamento dos Modelos (RQ3):**  
  - *Persistência:* Ao repetir 500 prompts que haviam causado alucinação, 58 % dos pacotes alucinados apareceram mais de uma vez nos 10 testes. Notavelmente, 43 % repetiram em **todas** as 10 (Figura 5 do artigo), e 39 % nunca repetiram. Isso indica que algumas alucinações são estáveis dentro de um modelo e podem ser exploradas.  
  - *Entre modelos:* Analisando quantos modelos distintos geraram cada nome de pacote, viu-se que **81 %** dos pacotes alucinados surgiram em apenas **1 modelo**. Ou seja, alucinações são em grande parte *específicas do modelo*. Mesmo modelos similares (ex.: GPT-3.5 vs GPT-4, ou versões do CodeLlama) tinham alucinações diferentes. (Figura 8).  
  - *Detecção de alucinações:* Testaram se cada modelo podia reconhecer suas próprias alucinações. GPT-4 Turbo, GPT-3.5 e DeepSeek acertaram >75 % das vezes em distinguir pacotes fictícios vs reais. A Tabela 2 mostra precisão/recall ≈0,8–0,9 para esses três modelos. (CodeLlama teve ~60–72 %, um pouco pior). Esse resultado inspirou o método de *auto-refinamento*.  

- **Características (RQ4):**  
  - *Semelhança semântica:* A distância de Levenshtein entre pacotes alucinados e seus vizinhos reais mostrou que a maioria não era “quase igual” a um pacote existente. Só 13,4 % tinham 1–2 caracteres de diferença; 48,6 % diferiam em 6 ou mais caracteres. E 20,2 % tinham distância ≥10 (praticamente sem semelhança). Isso sugere que as alucinações não são meras *typos* de nomes populares, mas criações substancialmente novas.  
  - *Pacotes removidos:* Identificaram 12.871 pacotes PyPI (2020–2022) que foram deletados até 2024. Apenas **133** deles (0,17 %) foram gerados nos testes. Ou seja, modelos raramente recomendam pacotes agora removidos. Conclusão: alucinações não vêm principalmente de pacotes obsoletos no treino.  
  - *Cross-language:* Das alucinações identificadas em testes de Python, apenas o ecossistema JavaScript contribuiu significativamente: 8,7 % dos nomes fictícios em Python eram pacotes reais do npm. Outras linguagens (R, Rust, etc.) somaram juntos apenas 0,8 %. Isso indica que parte das “alucinações” é o modelo confundindo contextos, especialmente entre Python/JS.

- **Mitigações (RQ5):**  
  Testaram três estratégias: RAG, auto-refinamento e fine-tuning (cada uma isoladamente e em conjunto). Focaram em dois modelos de perfis distintos: *DeepSeek 6.7B* (bom desempenho) e *CodeLlama 7B* (pior). Os resultados estão na Tabela abaixo (taxa de alucinação):

  | Técnica                   | DeepSeek 6.7B | CodeLlama 7B |
  |---------------------------|--------------:|-------------:|
  | **Sem mitigação**         |        16.14% |       26.28% |
  | **RAG**                   |        12.24% |       13.40% |
  | **Auto-Refinamento**      |        13.04% |       25.51% |
  | **Fine-Tuning**           |         2.66% |       10.27% |
  | **Ensemble (todas 3)**    |         2.40% |        9.32% |

  (Taxas obtidas empiricamente; sem ajustes estatísticos formais de significância, dado o uso massivo de amostras). Em resumo:
  - Todas as técnicas reduziram o problema, mas com diferenças. RAG já corta quase ¼ das alucinações iniciais. O fine-tuning foi **mais drástico** (DeepSeek caiu 83%, CodeLlama 61%).
  - O ensemble (usar todas em sequência) teve o maior efeito agregado, removendo ~85% (DeepSeek) e 64% (CodeLlama) de alucinações.
  - **Custos:** O fine-tuning piorou a qualidade do código. Em HumanEval, o *pass@1* do DeepSeek caiu de 51,4% para 25,3% (decr. 26,1 pontos); o CodeLlama foi de 19,6% para 16,4%. Em contrapartida, após fine-tuning esses valores ainda ficam no patamar de modelos respeitados (p.ex. Mistral 7B ~26%). Os autores destacam que é necessário pesquisar fine-tuning que não prejudique tanto a funcionalidade, mas notam que RAG e auto-refinamento não tiveram impacto negativo no código.  

## Discussão dos Autores  
Os autores enfatizam que *package hallucinations* são um fenômeno **sistêmico** em LLMs de código. Mesmo o melhor modelo (GPT-4T) apresentou alucinações repetidas (embora baixas). A persistência dentro do modelo (58% repetição) sugere que atacantes podem descobrir nomes alucinados consistentes e explorá-los. O fato de a maioria dos nomes ser específica de cada modelo dificulta generalizar uma defesa por blacklist única. Também destaca-se que alucinações não são apenas “typos” (a maioria difere bastante de nomes reais).  

Eles apontam que o ambiente de *devOps* precisa incorporar verificações automáticas de pacotes sugeridos pela IA. Estratégias superficiais (como filtrar por lista de pacotes válidos) são falhas, pois um atacante pode simplesmente publicar o nome fictício. Em vez disso, as abordagens de pré-verificação (RAG com base de pacotes reais) ou revisão ativa (auto-refinamento) mostraram ser eficazes. No entanto, concluem que **nenhuma mitigação é infalível** sem custos. O estudo serve como alerta de que, ao usar IA para programar, confiar cegamente em pacotes recomendados é perigoso. 

## Limitações e Validade  
- **Interna:** A validade interna é forte: amostras massivas (576k códigos), múltiplos modelos, repetição de prompts e uso de heurísticas robustas. O procedimento de detecção de pacotes (3 heurísticas) é cuidadoso. Dados e código foram disponibilizados (Zenodo/GitHub), indicando reprodutibilidade. A análise estatística é básica (percentuais), mas sobre um volume enorme de dados.
- **Externa:** O estudo foi feito com modelos de meados de 2024 (GPT-4 Turbo, CodeLlama, etc.). Modelos muito novos (ex.: GPT-4o 2025, Llama3, Mistral 2) podem comportar-se diferente. Testaram poucos modelos comerciais (fator financeiro), então comparar “comercial vs open-source” não significa que todos comerciais serão melhores. Além disso, o estudo **não envolveu usuários humanos reais**. Não mede quantos desenvolvedores instalariam pacotes sugeridos pela IA; assume-se cegamente a confiança do usuário. Assim, aplica-se a *possibilidade técnica* de ataque, mas não avalia o risco real em uso no mundo real, que depende de fatores humanos (checagem manual, pipelines de CI, políticas corporativas, etc.). Ademais, a detecção de pacotes tem limitações (p.ex. se o modelo gerasse só import sem “install”, as heurísticas podem perder casos). Os autores ressaltam que o valor de 19,7 % é um *mínimo* observável pelas ferramentas usadas.

## Implicações para Segurança da Cadeia de Suprimentos  
Este trabalho amplia o escopo de preocupações de segurança com IA em código. Até então, trabalhos focavam em vulnerabilidades inseridas no código (p.ex. SQL injections no output da IA) ou no comportamento do desenvolvedor com IA (Pearce *et al.* 2022; Perry *et al.* 2023). Spracklen *et al.* mostram que a IA também pode gerar **novos vetores de ataque** de supply chain: pacotes *inventados*. O fluxo de ataque é simples: a IA produz um nome falso, o atacante registra esse nome em PyPI/npm contendo malware, e futuros usuários da IA instalam-no sem saber. Esse cenário exige que ferramentas de segurança de software (análise de dependências) considerem o uso de IA. É recomendado:  
- Validar manualmente pacotes sugeridos, especialmente não populares.  
- Usar scanners de licenças/reputação (SCA) que apontem pacotes de baixa instalação ou suspeitos.  
- Políticas de lockfile e revisão de mudança de dependências mesmo em código gerado por IA.  
- Pesquisas futuras em **IA alinhada** poderiam focar em instruir modelos a “citar apenas pacotes existentes” (p.ex. via RAG).  


## Conclusões e Recomendações  
Este estudo pioneiro sobre *package hallucinations* demonstra que LLMs de programação, mesmo os atuais de ponta, têm tendência sistemática a inventar pacotes que não existem. Essa tendência é **suficientemente alta** (quase 20 % dos pacotes nas condições do experimento) para merecer atenção urgente. Os desenvolvedores devem tratar recomendações de IA com o mesmo escrutínio que fariam para código copiado da Internet: verificar existência e reputação dos pacotes antes de instalar. Ferramentas de segurança podem incorporar checagens automáticas de existência em repositórios, e alertas quando pacotes sugeridos têm poucos downloads ou são recém-criados.  

Para pesquisadores, os autores recomendam explorar mais: novos modelos, mitigação por design (p. ex., treinos com penalização de pacotes fictícios) e estudos envolvendo desenvolvedores reais. No futuro, é importante estender esse tipo de análise a outros ecossistemas (ex.: Java/Maven) e modos de IA (ex.: Copilots que sugerem imports dinamicamente). Este trabalho mostra que, **na transição para codificação assistida por IA, ganhar produtividade exige também repensar a segurança de nossa cadeia de software**.

---

**Fontes:** Todos os dados quantitativos e afirmações acima baseiam-se nos resultados de Spracklen *et al.* (2025).  (Outros artigos citados foram usados comparativamente, mas as citações acima se referem ao material principal.)  

**Figura – Fluxo de Ataque por Alucinação de Pacotes (mermaid):**  

```mermaid
flowchart LR
  U[Usuário solicita código] --> LLML[LLM de código]
  LLML --> C{Gera código com pacote fictício}
  C --> A[Atacante observa nome fictício]
  A --> P[Atacante publica pacote malicioso]
  P --> U2[Próximo usuário solicita código similar]
  U2 --> LLML2[LLM (mesmo tipo) gera código]
  LLML2 --> P2{Recomenda pacote malicioso}
  P2 --> U3[Usuário instala pacote malicioso!]
```  

**Tabelas resumidas:** (Taxas de alucinação por modelo/linguagem e eficácia das mitigações foram discutidas no texto e fontes citadas.)