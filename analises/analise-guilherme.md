# IA e Segurança de Software

## 1. Contexto Institucional e Enquadramento do Tema

A analise aborda a pergunta principal prescrita para o Tema 3 — *"Usar IA para programar deixa o software mais inseguro?"* — desdobrando-a em suas três perguntas norteadoras: a origem estrutural das vulnerabilidades (código, dependências e fator humano), os elementos de segurança sistematicamente negligenciados pelos desenvolvedores sob assistência automatizada, e a relação de proporcionalidade entre velocidade de produção de código e rigor de auditoria de segurança.

---

## 2. De Onde Vêm as Vulnerabilidades:

A determinação da origem das falhas de segurança em sistemas construídos com o suporte de Modelos de Linguagem de Grande Porte (LLMs) exige a decomposição do risco em três camadas interdependentes: a geração direta de código-fonte vulnerável, a recomendação de dependências inexistentes na cadeia de suprimentos e o comportamento de aceitação por parte dos profissionais de desenvolvimento.

A tríplice estrutura do risco articula-se da seguinte forma:

1. **Vulnerabilidade Direta no Código (Camada Técnica)**: Mapeada por Pearce et al. (IEEE S&P, 2022), resulta na introdução de falhas de lógica, gestão de memória e validação diretamente nas instruções geradas pelos modelos.
2. **Insegurança da Cadeia de Suprimentos (Camada de Infraestrutura)**: Identificada por Spracklen et al. (USENIX Security, 2025), manifesta-se na alucinação recorrente de pacotes e bibliotecas não existentes em repositórios públicos.
3. **Viés de Automação e Sobreconfiança (Camada Comportamental)**: Demonstrada por Perry et al. (ACM CCS, 2023), consiste na redução do rigor de revisão por parte do desenvolvedor, alimentada pela ilusão de correção das sugestões automatizadas.

### 2.1 Vulnerabilidades Diretas no Código-Fonte Gerado

A análise empírica desenvolvida por Pearce et al. (IEEE S&P, 2022) investigou a prevalência de fraquezas de segurança do catálogo MITRE Top 25 Common Weakness Enumeration (CWE) nas sugestões produzidas pelo GitHub Copilot. Através de uma metodologia estruturada em 89 cenários de teste que geraram 1.689 programas válidos, os autores demonstraram que 40,4% de todos os códigos completados pela ferramenta continham vulnerabilidades de segurança diretamente exploráveis.

A distribuição dessas falhas varia substancialmente conforme a linguagem de programação e o domínio do software:

- **Linguagem C**: Apresentou a maior taxa de vulnerabilidade, atingindo 50,29% de programas inseguros entre as 513 amostras analisadas, com 52,00% das sugestões de maior pontuação (*top-scoring suggestions*) contendo falhas graves de memória e ponteiros (Pearce et al., 2022).
- **Linguagem Python**: Registrou 38,35% de soluções vulneráveis em 571 programas, destacando-se falhas de validação de entrada, desserialização insegura de dados e gerência de caminhos de arquivo (Pearce et al., 2022).
- **Domínio de Hardware (Verilog RTL)**: Na avaliação de CWEs específicas de sistemas embarcados e circuitos integrados, a taxa de sucesso na geração de código seguro reduziu-se drasticamente à medida que a complexidade do prompt aumentava. Para a fraqueza CWE-1234 (sobrescrita de registradores bloqueados em modo de depuração), todas as sugestões de pontuação mais alta produzidas para cenários complexos resultaram em código vulnerável (Pearce et al., 2022).

Entre as vulnerabilidades mais recorrentes mapeadas no estudo de Pearce et al. (2022), destacam-se:

- **CWE-22 (Improper Limitation of a Pathname to a Restricted Directory / Path Traversal)**: Presente em 60% dos cenários gerais e em 100% das sugestões de maior relevância geradas pelo modelo para manipulação de arquivos compactados e servidores web em Python e C.
- **CWE-787 (Out-of-bounds Write / Buffer Overflow)**: Amplamente reproduzida em C pela utilização de funções de formatação de string desprovidas de verificação de limites, como `sprintf`, que pode gerar cadeias de até 317 caracteres a partir de especificadores de ponto flutuante, estourando buffers alocados com dimensões inferiores.
- **CWE-798 (Use of Hard-coded Credentials)**: Inserção automática de senhas, chaves criptográficas e tokens de acesso fictícios no próprio corpo do código-fonte durante o preenchimento de conexões com bancos de dados e APIs externas.

### 2.2 Alucinação de Dependências na Cadeia de Suprimentos

A segunda dimensão do risco desloca-se do código autoral para a infraestrutura de dependências externas. O estudo conduzido por Spracklen et al. (USENIX Security, 2025) realizou a primeira avaliação em larga escala sobre o fenômeno da alucinação de pacotes (*package hallucinations*) em LLMs voltadas à geração de código. Analisando 16 modelos distintos (incluindo arquiteturas comerciais fechadas e modelos *open-source*) submetidos a 576.000 amostras de código em Python e JavaScript, o estudo contabilizou a geração de 2,23 milhões de recomendações de pacotes.

Os dados quantitativos obtidos por Spracklen et al. (2025) revelam a dimensão sistêmica do problema:

- **Taxa Média de Alucinação**: 19,7% de todas as recomendações de pacotes geradas pelas LLMs referem-se a bibliotecas inexistentes nos ecossistemas oficiais PyPI (Python Package Index) e npm (Node Package Manager), totalizando 440.445 pacotes alucinados, dos quais 205.474 representam nomes únicos fictícios.
- **Divergência entre Arquiteturas**: Modelos comerciais fechados da série GPT registraram uma taxa média de alucinação de 5,2%, com o GPT-4 Turbo obtendo o menor índice geral de 3,59%. Em contrapartida, modelos *open-source* apresentaram uma taxa média de alucinação de 21,7%, atingindo 26,1% no CodeLlama 7B.
- **Disparidade por Ecossistema**: A geração de código em JavaScript via npm apresentou uma taxa de alucinação significativamente superior (21,3%) em comparação com o ecossistema Python no PyPI (15,8%).

### 2.3 O Comportamento Humano como Elemento Integrador

A terceira fonte de vulnerabilidades reside na atitude dos profissionais de engenharia de software em relação aos sistemas de recomendação automatizados. Como demonstrado empiricamente por Perry et al. (ACM CCS, 2023), o fator humano atua como a ponte que transforma vulnerabilidades teóricas em código implantado em ambientes de produção. A delegação passiva de tarefas de codificação — fenômeno impulsionado pela automação inteligente — reduz o engajamento cognitivo durante as etapas de teste e revisão, consolidando o ciclo do risco triplo.

---

## 3. Fator Humano e o Paradoxo da Sobreconfiança: O Que os Desenvolvedores Deixam de Verificar

Para responder à segunda pergunta norteadora — *"Quando um desenvolvedor confia numa sugestão, o que ele está deixando de verificar?"* —, é necessário examinar o estudo experimental de usabilidade e segurança realizado por Perry et al. (ACM CCS, 2023). A pesquisa avaliou o comportamento de 47 desenvolvedores divididos entre um grupo de controle (sem acesso à IA) e um grupo experimental (com acesso a assistentes baseados na arquitetura OpenAI Codex) durante a execução de cinco tarefas de programação com requisitos críticos de segurança.

### 3.1 Mapeamento Contrafactual das Falhas de Verificação

A análise qualitativa e quantitativa das submissões efetuada por Perry et al. (2023) identificou omissões específicas de validação técnica em quatro das cinco tarefas propostas, demonstrando que a presença da IA reduz a probabilidade de escrita de código seguro de 29% (grupo de controle) para apenas 12% (grupo experimental):

### 3.2 A Inversão da Autopercepção de Segurança

O achado comportamental mais expressivo revelado por Perry et al. (2023) refere-se ao descompasso entre a segurança real do código produzido e a percepção de segurança manifestada pelos participantes em pesquisas Likert pós-teste:

> "Desenvolvedores que produziram soluções com falhas graves de segurança reportaram níveis significativamente mais altos de confiança na integridade de seu código (média de 4.0 em uma escala Likert de 5 pontos) do que os participantes do grupo de controle que escreveram código seguro (média entre 1.5 e 2.0)." (Perry et al., ACM CCS, 2023).

Essa inversão da autopercepção comprova que os assistentes de IA geram uma ilusão de correção técnica. Como o código sugerido apresenta alta sintaxe fluente e compila sem erros aparentes, o desenvolvedor pressupõe a presença de defesas internas que a IA não implementou.

---

## 4. Velocidade de Produção Versus Rigor de Segurança: O Antagonismo na Prática

Ao examinar a terceira pergunta norteadora — *"A velocidade de produzir código e a segurança do que se produz caminham juntas ou em direções opostas?"* —, os dados empíricos indicam uma relação de proporcionalidade inversa entre a taxa de geração de código e a densidade de defeitos de segurança não detectados.

### 4.1 A Distância de Edição Humana como Indicador de Segurança

O estudo de Perry et al. (2023) analisou a distância de edição normalizada entre as sugestões brutas fornecidas pela IA e o código final submetido pelos participantes. A distribuição das submissões revelou que:

- **87% dos códigos classificados como estritamente seguros** pertencia a participantes que aplicaram modificações manuais profundas, reestruturando a lógica e alterando a assinatura das funções propostas pela IA.
- Participantes que realizaram edições superficiais ou que copiaram diretamente o bloco de código sugerido obtiveram uma taxa desproporcional de soluções vulneráveis.

A aceleração do fluxo de trabalho obtida pela aceitação imediata de sugestões anula o tempo de reflexão necessário para a identificação de casos de borda e falhas de validação.

### 4.2 O Impacto dos Parâmetros de Geração e Ciclos de Erro

A taxa de código inseguro gerado por LLMs está fortemente associada às configurações de amostragem do modelo e à estrutura das instruções fornecidas:

1. **Temperatura de Amostragem ($T$)**: A temperatura controla a aleatoriedade da distribuição de probabilidade dos tokens emitidos. Spracklen et al. (2025) e Perry et al. (2023) observaram que a elevação da temperatura ($T > 1,0$) resulta em um crescimento exponencial na taxa de alucinação de pacotes e na geração de trechos de código não determinísticos. No estudo de Perry et al. (2023), participantes que mantiveram a temperatura padrão sem restringir a busca obtiveram taxas de código inseguro entre 39% e 81% em determinadas tarefas.
2. **Ciclos de Realimentação de Erro (*Model Close Prompts*)**: Perry et al. (2023) identificaram que **61% dos desenvolvedores** reutilizaram outputs defeituosos da IA como contexto para as solicitações subsequentes. Esse comportamento estabelece um ciclo de realimentação positiva no qual a IA consolida a premissa vulnerável introduzida nas interações anteriores, dificultando a convergência para um algoritmo seguro.

## 5. Conclusao: Usar IA Deixa o Software Mais Inseguro?

Com base na síntese analítica dos dados quantitativos extraídos da literatura de referência, a resposta à pergunta principal do Tema 3 é:

> **Sim, o uso de assistentes de inteligência artificial na programação torna o software resultante significativamente mais inseguro, não apenas pelas deficiências estatísticas intrínsecas dos modelos de linguagem, mas primordialmente pelo Risco Triplo decorrente do viés de automação e da sobreconfiança humana.**

A sustentação dessa conclusão apoia-se em três pilares estatísticos consolidados:

1. **Insegurança do Código Gerado**: Aproximadamente **40,4% do código** produzido por ferramentas de ponta contém vulnerabilidades exploráveis do catálogo MITRE Top 25 CWE, atingindo mais de 50% em linguagens de baixo nível como C (Pearce et al., IEEE S&P, 2022).
2. **Vulnerabilidade da Cadeia de Suprimentos**: Cerca de **19,7% das recomendações de pacotes** de software consistem em dependências inexistentes e altamente repetíveis (58% de persistência), expondo o ecossistema a ataques em massa de *Slopsquatting* (Spracklen et al., USENIX Security, 2025; Socket.dev, 2025).
3. **Degradação da Verificação Humana**: A utilização de assistentes reduz a proporção de código seguro entregue por desenvolvedores de **29% para 12%**, enquanto induz uma falsa percepção de segurança que eleva a autoconfiança para pontuações médias de 4,0/5,0 em soluções comprovadamente defeituosas (Perry et al., ACM CCS, 2023).

Portanto, a IA atua como um amplificador de velocidade para a produção de código não auditado. Sem a imposição de barreiras de contenção estruturadas, os ganhos imediatos de produtividade são consumidos pela remediação de incidentes e brechas de segurança.

## Referências Bibliográficas

- PEARCE, H.; AHMAD, B.; TAN, B.; DOLAN-GAVITT, B.; KARRI, R. Asleep at the Keyboard? Assessing the Security of GitHub Copilot’s Code Contributions. In: *IEEE Symposium on Security and Privacy (S&P)*, 2022, pp. 754-768.
- PERRY, N.; SRIVASTAVA, M.; KUMAR, D.; BONEH, D. Do Users Write More Insecure Code with AI Assistants? In: *Proceedings of the 2023 ACM SIGSAC Conference on Computer and Communications Security (CCS '23)*, 2023, pp. 2785–2799.
- SOCKET. *The Rise of Slopsquatting: How AI Hallucinations Are Fueling a New Class of Supply Chain Attacks*. Blog Socket.dev, 08 abr. 2025. Disponível em: <https://socket.dev/blog/slopsquatting-how-ai-hallucinations-are-fueling-a-new-class-of-supply-chain-attacks>. Acesso em: 05 out. 2026.
- SPRACKLEN, J.; WIJEWICKRAMA, R.; SAKIB, A. H. M. N.; MAITI, A.; VISWANATH, B.; JADLIWALA, M. We Have a Package for You! A Comprehensive Analysis of Package Hallucinations by Code Generating LLMs. In: *USENIX Security Symposium*, 2025.
- UNIVERSIDADE DE CAXIAS DO SUL (UCS). *Diretrizes do Trabalho Avaliativo 1 (T1) — Disciplina ENS4000: Gerência de Configuração*. Caxias do Sul: UCS, 2025.
