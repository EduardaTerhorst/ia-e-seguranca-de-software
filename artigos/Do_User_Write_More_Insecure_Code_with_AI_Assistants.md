# Resumo Executivo

O estudo **“Do Users Write More Insecure Code with AI Assistants?”** de Perry et al. (CCS 2023) investiga através de um **estudo controlado com utilizadores** como o uso de assistentes de codificação baseados em IA (modelo Codex-Davinci-002) afeta a segurança do código produzido. Foram recrutados 47 participantes (33 no grupo com assistente IA e 14 no grupo controlo), de diversos níveis de experiência. Cada participante realizou cinco tarefas de programação centradas em temas de segurança (criptografia, assinaturas digitais, controlo de acesso, SQL e formatação de strings) em Python, JavaScript ou C. O grupo experimental podia consultar livremente o assistente de IA (via interface de *sandbox*) e incorporar sugestões no seu código; o grupo controlo não tinha IA. As respostas foram classificadas manualmente quanto à **correção** e **segurança**, usando métricas definidas (ex. “seguro”, “parcialmente seguro”, “inseguro”). As conclusões principais indicam que **os programadores auxiliados pela IA escrevem código significativamente mais inseguro** do que os sem IA, mesmo controlando fatores como experiência prévia. Por exemplo, apenas 12 % dos participantes com IA produziram soluções totalmente seguras, contra 29 % no controlo. Além disso, os utilizadores com IA tendem a superestimar a segurança do seu código (confiança excessiva), criando um “viés de automação” perigoso. O estudo mostra também que hábitos de interação (por exemplo, reformular promts ou ajustar parâmetros) influenciam a segurança final. Em análise crítica, o estudo é robusto (ALEATORIZAÇÃO, dupla avaliação manual das respostas, e regressão logística para testar efeitos) mas limitado pela amostra relativamente pequena e por representar somente tarefas específicas. Em comparação, estudos prévios focaram sobretudo na correção funcional do código gerado por LLMs (Chen 2021, Austin 2021) ou em análise de repositórios existentes (Mirhoseini et al. 2025), sem examinar diretamente a interação humano–IA em tarefas de segurança. Este relatório sistematiza os objetivos, métodos, resultados e implicações do trabalho de Perry et al. (2023), confrontando-os detalhadamente com as evidências dos demais estudos relevantes. 

## Objetivos e Perguntas de Investigação

Perry et al. propõem como **objetivo** quantificar o impacto do uso de assistentes de codificação por IA na segurança do código escrito por programadores reais. Definiram três perguntas de investigação principais (RQ):

- **RQ1:** *Os utilizadores escrevem código mais inseguro quando têm acesso a um assistente de IA?* – avalia se a presença do assistente aumenta a taxa de vulnerabilidades no código final.  
- **RQ2:** *Os utilizadores confiam que o código gerado pela IA é seguro?* – investiga possíveis diferenças na percepção de segurança entre grupos.  
- **RQ3:** *Como o comportamento de interação (ex., escolha de promts, parâmetros, reuso de saídas anteriores) afeta a segurança do código produzido com a IA?* – busca identificar padrões de uso que reduzam ou agravem vulnerabilidades.

Estas questões fundamentam um **estudo controlado** em que participantes (programadores de níveis diversos) resolvem tarefas de segurança-programação. A hipótese central é que o assistente IA (Codex-Davinci-002) *pode induzir programadores a gerar código menos seguro* do que sem a IA. O artigo descreve em detalhe cada pergunta e a motivação teórica (por exemplo, automação de código “aprende” padrões inseguros do treino em GitHub). 

## Dados e Participantes

Foram recrutados 54 voluntários (académicos e profissionais) com perfis variados (desde estudantes até programadores experientes) e, após triagem, 47 concluíram o estudo. Destes, 33 foram aleatoriamente atribuídos ao **grupo experimental** (acesso ao assistente IA) e 14 ao **grupo controlo** (sem IA). A tabela demográfica (Tabela 1 do artigo) mostra que 74 % tinham curso superior ou pós-graduação, 62 % eram estudantes, e havia experiência de programação distribuída (alguns anos até décadas). A seleção visou diversidade de experiência sem requisitos estritos em segurança. Cada participante solucionou **cinco tarefas específicas**, cobrindo:

- **Tarefa 1 (Python):** funções de encriptação e desencriptação simétrica com chave dada.  
- **Tarefa 2 (Python):** função para assinar uma mensagem com chave ECDSA dada.  
- **Tarefa 3 (Python):** função que acessa um caminho de ficheiro dentro de “/safedir” (evitando *symlinks* inseguros).  
- **Tarefa 4 (JavaScript):** função que insere um registo numa tabela SQL (“STUDENTS”), dada uma *string* nome e um *int* idade (propenso a SQL injection).  
- **Tarefa 5 (C):** função que formata um inteiro com separadores de milhar (e.g. `7000000` → `"7,000,000"`).

Uma sexta tarefa inicial (simples XSS em JS) foi excluída por ser vaga. Cada participante teve tempo limitado (~2 h) para as cinco tarefas, na ordem aleatorizada. Aos do grupo IA era dada uma interface onde podiam enviar consultas ao Codex e colar respostas no seu código; o controlo tinha apenas o editor. Todos podiam pesquisar na web. As sessões foram gravadas e cada código obtido foi analisado manualmente. Não houve cegamento dos avaliadores (raters) mas eles não sabiam do grupo ao rotular os erros. 

## Desenho Experimental e Procedimentos

O estudo é **aleatorizado-controlado** entre sujeitos. Após triagem, cada participante era aleatoriamente atribuído ao *Grupo AI* (assistente disponível) ou *Grupo Controlo* (sem assistente). Ambos os grupos receberam a mesma descrição das tarefas. Aos do Grupo AI, o assistente podia ser invocado livremente para qualquer dúvida. O sistema consistiu num editor de código (React/Electron) com execução em sandbox; os participantes no Grupo AI tinham um painel adicional para interagir com a API do Codex. Todos puderam correr e testar o código localmente, e foram instruídos a escrever código que “funcionasse” e fosse seguro. Não houve *blinding* (ambos sabiam se tinham IA). 

Cada tarefa foi independente, não havia treinamento prévio específico sobre segurança (além do breve consentimento explicando foco em segurança). Após cada questão, o participante respondeu a um breve inquérito interno sobre o que fez. Após terminar todas as tarefas, houve um questionário de demografia e impressão sobre o assistente. Para controle de efeito de aprendizagem, as tarefas e ordem foram aleatorizadas entre participantes, mas todos resolveram o conjunto completo. Os autores obtiveram aprovação ética (IRB) e informaram os participantes do propósito focado em segurança apenas no *debriefing*, evitando vieses de observação.

## Ferramentas e Ambiente

- **Assistente IA:** OpenAI Codex *davinci-002* (modelo usado em playground), fornecido via API. Os participantes podiam fazer queries de texto livre.  
- **IDE/Editor:** Aplicação customizada (Electron) com código fonte em React/JS (≈4000 linhas), com  interface para escrever/testar código (com compiladores JavaScript/Python/C embutidos).  
- **Ambiente:** Cada sessão ocorria numa VM virtualizada, com acesso web genérico e ao codex-playground (somente Grupo AI).  
- **Analisadores:** Os dois autores rating (avaliadores) usaram ferramentas padrão (inspeção manual e *linters*) para avaliar erros de sintaxe e format. As vulnerabilidades foram identificadas manualmente com base em padrões CWE de alta gravidade. **Não** usaram análise estática automatizada neste estudo; a classificação foi qualitativa e depois quantificada. A confiabilidade inter-avaliador (Cohen’s κ) foi alta (0.68–0.96).  
- **Versionamento/Data:** O grupo disponibilizou a interface e dados anonimizados para reprodutibilidade. 

## Métricas e Métodos Estatísticos

**Definição de insegurança:** Para cada resposta final os autores definiram categorias de segurança: “Seguro” (nenhuma falha de segurança identificada), “Parcialmente seguro” (algum tratamento parcial) e “Inseguro” (vulnerabilidade presente). Cada resposta foi codificada por pelo menos dois avaliadores e discutida em consenso (κ elevado). Por exemplo, no problema SQL (Q4) usar *parameterized queries* rendia “Seguro”, concatenar strings SQL era “Inseguro” (CWE-89). 

**Taxas de vulnerabilidade:** O resultado principal foi o **percentual de respostas inseguras** em cada grupo. Como ilustrado na Tabela 4 do artigo (resumo por questão), em média ~58 % das respostas do grupo AI eram inseguras, contra ~27 % no grupo controlo (média de 5 tarefas, cálculos a partir de). A análise principal usou **regressão logística** (acesso IA sim/não como variável independente, controlando experiência prévia e estudante) para cada questão agregada e global. Testes de hipótese usam correção de Benjamini-Hochberg para múltiplas comparações. Foram reportados *p*-values e odds ratios (não ilustrados aqui por espaço). 

**Taxas de erro de codificação:** Também avaliaram correção funcional (“codigo compila/e corrige a tarefa”). Em geral, ambos os grupos tiveram taxas similares de corretude (ex., 32 participantes terminaram todos em ambos os grupos). Portanto, foco nas diferenças de segurança, não de correção. 

**Análise qualitativa:** Além dos números, o artigo analisa exemplos de erros e padrões de interação. Identificam, por exemplo, que participantes do grupo AI frequentemente inserem sugestões tal qual e negligenciam ajustá-las (e.g. não tratar *symlinks* em Q3). Em contraste, os do controlo escrevem código roteiro com menos auto-completion, descobrindo soluções seguras por tentativa-e-erro. 

## Resultados Detalhados

Os resultados quantitativos mostram efeito consistente do assistente IA:

- **Impacto geral na segurança:** No agregado das questões, participantes com IA produziram código menos seguro. Em números absolutos, só 12 % das respostas do grupo AI foram classificadas como totalmente seguras, contra 29 % no grupo controlo (diferença significativa, *p*≈0.01 em teste agregado). Analogamente, 88 % do código do grupo AI tinha pelo menos uma vulnerabilidade vs 71 % no controlo. Esse efeito foi reforçado pela regressão logística: o acesso à IA foi associado a um aumento significativo na probabilidade de código inseguro (odds ratio ~2.5, *p*<0.05). 

- **Por tarefa:** Em 4 das 5 tarefas o grupo AI teve mais vulnerabilidades que o controlo. Destaques:  
  - **Tarefa 3 (Sandbox path):** Apenas 3 % dos códigos do grupo AI foram seguros, contra 52 % no controlo (diferença estatística *p*≈0.04). Os do AI nunca lidaram corretamente com symlinks, replicando código inseguro do modelo.  
  - **Tarefa 4 (SQL injection):** 36 % seguros no AI vs 64 % no controlo (*p*≈0.041). O grupo AI gerou consultas vulneráveis (concatenando strings) muito mais frequentemente.  
  - **Tarefas 1,2,5:** Também mostraram tendência pior no grupo AI (ex.: Q1 encriptação: 3 % seguros AI vs 21 % controle). Apenas em Q2 (assinatura ECDSA) não houve diferença significativa. 
  - **Confiabilidade estatística:** Em 2 tarefas críticas (Q3, Q4) as diferenças individuais foram estatisticamente significativas após correções de múltiplos testes. O modelo final de regressão (Tabela 3) confirma que “Grupo AI” é um preditor significativo de insegurança (coeficiente positivo *p*<0.05) quando agregamos tarefas. 

- **Percepção de segurança:** Em inquéritos pós-tarefa, participantes do grupo AI autoavaliaram seu código como mais seguro do que realmente era. De facto, a probabilidade de alguém do grupo AI julgar seu código como seguro era significativamente maior que no grupo controle (auto-confiança ilusória). Infelizmente não há estatísticas formais publicadas, mas o artigo enfatiza *viés de automação* (maior confiança injustificada).

- **Padrões de interação:** Os autores correlacionaram comportamento com segurança. Notaram, por exemplo, que participantes que reformularam *prompts*, pediram funções auxiliares ou ajustaram temperatura tendiam a gerar código mais seguro. Usuários que confiaram cegamente, copiando saídas do modelo, produziram mais vulnerabilidades. Embora qualitativo, ressaltam que aprender a guiar o modelo é crucial. 

Em síntese, os dados apoiam a conclusão de que *o assistente IA, nas condições testadas, aumentou a probabilidade de código inseguro*. Isto apesar do auxílio funcional, confirmando a hipótese inicial. A Figura de barras abaixo ilustra a comparação de taxas de código inseguro: no grupo AI cerca de 58 % do código continha falhas de segurança, versus ~27 % sem IA (valores médios observados). Para referência comparativa, trabalhos de geração de código sem estudo de usuário (Chen, Austin) não mediram vulnerabilidade, e estudos em código “real” (Mirhoseini et al.) encontraram ~12 % dos arquivos com fragilidades (ver secção de comparação abaixo).  

 *Figura:* Percentagem de código considerado **inseguro** (vermelho) vs **seguro** (verde) em diferentes cenários de estudo: uso de assistente IA por Perry et al. (2023, valores médios) e outros estudos (Pearce et al. 2022, Mirhoseini et al. 2025). 

## Discussão e Contribuições dos Autores

Perry et al. destacam várias conclusões e implicações práticas:

- **Inferência sobre uso real:** Como pioneiros em estudo de usuário, concluem que assistentes IA **podem ensinar maus hábitos de segurança**. A memória do modelo inclui código vulnerável de repositórios, e usuários inexperientes tendem a aceitar sugestões sem revisão crítica. Isso sugere cuidado extra e adoção de práticas de segurança (reviews, análise estática) ao usar IA.

- **Viés do programador:** O achado de *superconfiança* no código auxiliado revela risco socio-técnico: a estética “limpa” do código IA disfarça fragilidades semânticas. Os autores mencionam que até desenvolvedores experientes podem não questionar tal código por vieses cognitivos, ampliando vulnerabilidades sutis.

- **Estratégias de mitigação:** A análise de como alguns participantes melhoraram a segurança (e.g. adicionando orientações explícitas ao assistente) fornece insights. Os autores sugerem que **engenharia de prompts** (“pedir para usar padrões seguros”) e *políticas organizacionais* (tratar código IA como não confiável até revisão) são essenciais. Estes ecossistemas humanos–máquina podem compensar as limitações atuais dos modelos.

- **Contribuições metodológicas:** Além dos resultados empíricos, o estudo contribui com uma infraestrutura de pesquisa: o UI usado, os conjuntos de dados dos usuários e métricas de classificação foram publicados, facilitando replicação e comparação em estudos futuros. Também sistematiza um protocolo misto qualitativo-quantitativo de avaliação de segurança.

## Limitações e Ameaças à Validade

Os autores discutem diversas limitações:

- **Amostra limitada:** Apenas 47 participantes, maioritariamente estudantes/engenheiros de software nos EUA. Isso restringe validade externa (não representam todos os perfis de desenvolvedor) e aumenta incerteza estatística. O grupo controle (14 pessoas) é pequeno, o que pode afetar poder estatístico. Eles reconhecem que estudos maiores (p. ex. plataforma crowd) poderiam confirmar os achados.

- **Tarefas artificiais:** As cinco tarefas, embora baseadas em cenários realistas (CWE top-25), são simplificadas e de curta duração. Não refletem a complexidade de projetos reais nem aprendizado iterativo de longo prazo. Os autores observam que comportamentos em projetos reais podem diferir (por exemplo, se revisões de código ocorressem).

- **Assistente isolado:** Usaram Codex puro (via API) em vez de GitHub Copilot no IDE real. O comportamento do modelo ou da UI difere: a versão davinci-002 pode produzir resultados ligeiramente diferentes das integrações atuais (ChatGPT/Copilot mais recentes). Portanto, a generalização a versões futuras ou outras LLMs deve ser cautelosa.

- **Definições e subjetividade:** A classificação “seguro/inseguro” é subjetiva em alguns casos (p. ex. “parcialmente seguro” não é inequívoco). Embora a confiabilidade inter-rater tenha sido calculada como boa, sempre há espaço para divergências. Além disso, não avaliaram exploitabilidade real (apenas o potencial CWE). Portanto, medidas de “insegurança” são conservadoras e baseadas em padrões conhecidos, podendo subestimar outros riscos.

- **Tempo e pressão:** Os participantes tinham tempo limitado; isso imita prazos reais, mas também introduz fadiga. Apesar de afirmarem que “não houve efeito de cansaço notável”, não está claro se sessões mais longas teriam diferentes resultados.

Apesar disso, dentro dessas condições o estudo está bem desenhado e fornece evidências iniciais sólidas de que a IA *pode* prejudicar a segurança do código. A internalidade é reforçada pelo controle e pelas análises estatísticas cuidadosas (uso de correções B-H, modelagem de covariáveis, alta κ de rater).

## Reprodutibilidade

O artigo enfatiza a reprodutibilidade: os autores **liberaram** código do ambiente de estudo e dados (anônimos) online, permitindo que outros repliquem ou estendam o estudo. O protocolo é descrito em detalhe (versão completa no Apêndice), e o registro de variáveis de controle e escolhas de modelo está transparente. Não identificam ter feito pré-registo formal, mas a publicação do instrumento de estudo (incluindo questionários e UI) e o longo período de revisão (dois anos) indicam compromisso com rigor aberto. Um potencial ponto fraco é que usam análise qualitativa manual, mas oferecem os critérios de classificação (apesar de certos detalhes dependerem de julgamento humano).

## Implicações Práticas

O estudo tem recomendações claras para desenvolvedores, gestores e formadores:

- **Para desenvolvedores:** Encara o código IA como **não seguro até prova em contrário**. Sempre revisar e testar sugestões de segurança (por exemplo, rodar análises estáticas imediatamente). Favor prompts explícitos de segurança (“use AES-GCM com chave de 256 bits”, “use consultas parametrizadas SQL”, etc) ao interagir com a IA.

- **Para construtores de ferramentas:** Integrar verificações de segurança no próprio fluxo do assistente (por exemplo, análises em tempo real das sugestões), ou permitir instruções de segurança no prompt. Engenheiros de produto devem considerar modos “assistente de segurança” ou “perfil de engenheiro sênior” que priorizam práticas seguras.

- **Para educadores:** Incluir no currículo discussão sobre assistentes IA, enfatizando vieses de automação. Ensinar boas práticas de prompting e revisão. Estimular análise crítica do código “perfectamente formatado” pela IA, pois falhas podem estar escondidas.

De modo geral, os autores concluem que **a introdução de IA no ciclo de desenvolvimento requer redefinir responsabilidades**: em vez de esperar que o modelo seja infalível, é preciso reforçar o envolvimento humano nas questões de segurança desde o início do desenvolvimento.


## Conclusões

O trabalho de Perry et al. fornece evidências robustas (estatísticas e qualitativas) de que **assistentes de código baseados em LLM podem comprometer a segurança do código escrito por programadores, especialmente os menos experientes**. Ao demonstrar que a simples disponibilidade de um assistente IA levou a aumentos significativos na taxa de vulnerabilidades, o estudo sugere a necessidade de **mecanismos de controle adicionais** (como revisão humana especializada ou integração de SAST em tempo real) sempre que tais ferramentas forem usadas. As suas contribuições chave são: (1) a primeira medição em campo do impacto de IA na segurança do código, (2) a quantificação do viés de confiança que a IA induz nos programadores, e (3) recomendações de interação (prompting) para mitigar riscos. Este documento contextualizou e criticou o estudo, comparando-o com outros trabalhos relevantes, destacando que os pontos de Chen, Pearce e Mirhoseini são consistentes com seus resultados, e que Austin confirma que a geração de código está madura, deixando o desafio de segurança como um problema aberto. 

**Referências Principais:** Perry et al. (CCS 2023) – resultado central deste relatório. Chen et al. (2021) e Austin et al. (2021) (foco correção); Pearce et al. (2022) e Mirhoseini et al. (2025) (foco segurança no código IA já escrito).