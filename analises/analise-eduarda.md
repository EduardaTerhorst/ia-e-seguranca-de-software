# Pesquisa: O Uso de IA no Desenvolvimento de Software e seus Impactos na Segurança

## Pergunta Principal: Usar IA para programar deixa o software mais inseguro?

### Resultado e Análise
Sim. Com base nas conclusões reunidas pelos artigos de Pearce et al. (2022), Mirhoseini et al. (2025), Perry et al. (2023), Gooding (2025) e Spracklen et al. (2025), utilizar inteligência artificial para escrever código torna os sistemas estatisticamente mais inseguros. Isso acontece por três motivos principais:
1. A IA repete erros antigos que aprendeu na internet (conforme apontado por Pearce et al., 2022).
2. Os programadores passam a confiar demasiado na ferramenta e revalidam menos o código (estudado por Perry et al., 2023).
3. A IA inventa nomes de bibliotecas/programas que atacantes podem utilizar para infetar computadores (demonstrado por Gooding, 2025 e Spracklen et al., 2025).

### Evidências e Dados dos Artigos
* **Erros na IA:** No estudo de **Pearce et al. (2022)**, os autores constataram que entre **40% e 44%** de todos os códigos criados pelo GitHub Copilot apresentavam falhas de segurança conhecidas, sendo que na linguagem C essa taxa subiu para **50,29%**.
* **Erros em Projetos Reais:** No artigo de **Mirhoseini et al. (2025)**, ao analisar repositórios reais no GitHub, os investigadores identificaram que **11,4% das linhas de código** e **18,5% dos ficheiros Python** gerados por IA continham falhas de segurança ativas.
* **Efeito nos Programadores:** No trabalho de **Perry et al. (2023)**, ao testar programadores em cenários práticos, observou-se que apenas **12% dos participantes que usaram IA** criaram soluções totalmente seguras, em comparação com **29% no grupo sem IA**. No conjunto de todas as entregas com auxílio de IA, **88% dos projetos continham erros de segurança**.
* **Ataque com Pacotes Falsos (*Slopsquatting*):** Nos estudos combinados de **Gooding (2025)** e **Spracklen et al. (2025)** sobre a tendência da IA em inventar bibliotecas de código, **Spracklen et al. (2025)** analisaram 576.000 trechos de código e registaram que **19,7% das recomendações de pacotes feitas por 16 IAs eram inventadas**, totalizando **205.474 nomes falsos** que podem ser registados por cibercriminosos.

### Conclusão da Pesquisa
Como resumido pelos trabalhos de Pearce et al. (2022), Perry et al. (2023) e Spracklen et al. (2025), o uso de IA aumenta significativamente o risco de introduzir falhas na aplicação. O ganho inicial de velocidade esconde o perigo de inserir fragilidades conhecidas e dependências inexistentes no projeto.

---

## Pergunta Norteadora 1: De onde vêm as vulnerabilidades de um software: do código, das dependências ou das pessoas?

### Resultado e Análise
De acordo com a visão integrada dos artigos de Pearce et al. (2022), Perry et al. (2023), Gooding (2025) e Spracklen et al. (2025), as falhas surgem da combinação dos três fatores: do código, das dependências (bibliotecas de terceiros) e do fator humano. A IA agrava a situação nas três frentes: gera código com falhas, inventa dependências que não existem e induz as pessoas a confiarem sem verificar.

### Evidências e Dados dos Artigos
* **Origem no Código (Treinamento da IA):** Nos artigos de **Chen et al. (2021)** e **Pearce et al. (2022)**, explica-se que a IA reproduz maus hábitos aprendidos ao analisar código antigo na internet. **Chen et al. (2021)** observaram a IA a criar sistemas de segurança fracos (como encriptações e chaves frágeis), enquanto **Pearce et al. (2022)** destacaram a frequência de erros de gestão de memória em linguagens como C.
* **Origem nas Dependências (Invenção de Pacotes):** No artigo de **Spracklen et al. (2025)**, foram contabilizadas **440.445 ocorrências em que as IAs sugeriram pacotes inexistentes**. Complementando esta descoberta, o estudo de **Gooding (2025)** demonstrou que *"58% destes nomes inventados repetem-se de forma consistente quando se faz a mesma pergunta de novo"*. Isso significa que um atacante pode identificar estes nomes repetidos, criar pacotes maliciosos com esses nomes exatos e esperar que os programadores os instalem por engano.
* **Origem nas Pessoas (Confiança Cega):** No estudo experimental de **Perry et al. (2023)**, demonstrou-se que os programadores confiam no código gerado apenas porque este aparenta estar bem escrito. Numa tarefa de manipulação de ficheiros e acessos ao sistema, **97% dos programadores que usaram IA entregaram códigos inseguros** (apenas 3% conseguiram criar código seguro, contra 52% no grupo de controlo sem IA).

### Conclusão da Pesquisa
Como demonstram Perry et al. (2023) e Spracklen et al. (2025), o risco funciona numa reação em cadeia: a IA sugere um *código* com erros juntamente com uma *dependência* inventada, e o *programador* aceita tudo sem validar porque assume que a ferramenta é infalível.

---

## Pergunta Norteadora 2: Quando um desenvolvedor confia numa sugestão, o que ele está deixando de verificar?

### Resultado e Análise
Segundo as análises de Pearce et al. (2022), Perry et al. (2023) e Spracklen et al. (2025), ao aceitar a sugestão da IA sem questionar, o programador foca-se apenas em verificar se o código "funciona", deixando de auditar controlos de segurança, limites de memória, a existência real de bibliotecas e a proteção de dados sensíveis.

### Evidências e Dados dos Artigos
* **Conferir se os Pacotes Existem:** O artigo de **Spracklen et al. (2025)** alerta que os programadores não confirmam se o pacote recomendado é real. Os autores constataram que em **48,6% das invenções**, o nome sugerido era completamente diferente de qualquer pacote existente, e em **8,7% dos casos em Python**, a IA recomendou um pacote que só existia no ecossistema JavaScript.
* **Proteção contra Invasões (*SQL Injection*):** Na pesquisa de **Perry et al. (2023)**, verificou-se que **64% dos programadores com acesso à IA** deixaram passar código vulnerável a ataques em bases de dados por causa de concatenações inseguras de texto.
* **Erros na Primeira Opção da IA:** No estudo de **Pearce et al. (2022)**, comprovou-se que a primeira sugestão apresentada pela ferramenta costuma ser perigosa: em **44,4% dos cenários gerais** e em **52% das situações na linguagem C**, a opção principal recomendada pelo GitHub Copilot vinha acompanhada de falhas graves.
* **Segurança de Palavras-passe e Sistema:** As investigações de **Chen et al. (2021)** e **Perry et al. (2023)** revelaram que os programadores negligenciam a verificação de métodos ultrapassados, parâmetros de encriptação fracos ou acessos indevidos a ficheiros do sistema operacional propostos pela IA.

### Conclusão da Pesquisa
Como enfatizado por Perry et al. (2023), a confiança cega na IA faz com que o desenvolvedor deixe de atuar como auditor. Ele valida a funcionalidade aparente, mas deixa a aplicação vulnerável a invasões, fuga de dados e execução de código malicioso (Gooding, 2025; Pearce et al., 2022).

---

## Pergunta Norteadora 3: A velocidade de produzir código e a segurança do que se produz caminham juntas ou em direções opostas?

### Resultado e Análise
Com base nas descobertas de Chen et al. (2021), Perry et al. (2023), Gooding (2025) e Spracklen et al. (2025), velocidade e segurança caminham em direções opostas. Quanto mais rápido se tenta gerar código com recurso a IA, menor tende a ser a postura de segurança do produto final.

### Evidências e Dados dos Artigos
* **Código Rápido vs. Código Seguro:** Os artigos de **Chen et al. (2021)** e **Austin et al. (2021)** demonstram que os modelos de IA alcançam taxas elevadas na resolução de problemas lógicos (até **83,8% no benchmark MathQA-Python**). No entanto, o estudo prático de **Perry et al. (2023)** provou que esta agilidade reduziu a percentagem de programadores que entregaram código 100% seguro de **29% para apenas 12%**.
* **O Dilema de Corrigir a IA:** Nos trabalhos de **Gooding (2025)** e **Spracklen et al. (2025)**, os investigadores tentaram ajustar o modelo DeepSeek para eliminar a invenção de pacotes (reduzindo a taxa de alucinação de 16,14% para 2,66%). Contudo, ao implementar esta correção de segurança, *"a capacidade do modelo em resolver problemas funcionais de programação caiu para metade, passando de 51,4% para 25,3%"*.
* **Perguntas Curtas Geram mais Erros:** O estudo de **Pearce et al. (2022)** observou que a tentativa de acelerar o processo enviando comandos (*prompts*) curtos e sem restrições explícitas de segurança fez com que as sugestões vulneráveis a injeção SQL subissem para **37,35%**.

### Conclusão da Pesquisa
Como resumido pelos estudos de Perry et al. (2023) e Gooding (2025), escrever código mais depressa com IA cobra um preço elevado na segurança. Se o tempo ganho na escrita não for canalizado para uma revisão e auditoria rigorosas, o resultado final é apenas a entrega mais rápida de um software repleto de vulnerabilidades.