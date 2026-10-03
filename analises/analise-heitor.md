# IA e Segurança de Software

Todos os artigos citados como referência são categóricos na conclusão de que a utilização de inteligência artificial no desenvolvimento de softwares aumenta a inseguridade do código.

## De onde vem a insegurança?

As vulnerabilidades do código, assim como quando gerada por humanos, vêm de fragilidades conhecidas e exploráveis por pessoas mal intensionadas. Nos casos gerados por humanos, acabam sendo implementadas por falta de conhecimento ou atenção, e que acabam sendo replicadas pelos modelos de linguagem treinados com estes códigos. 

Em estudo onde os participantes receberam a tarefa de elaborar funções de criptografia e descriptografia de mensagens, aqueles que interagiram com a inteligência artificial foram mais propensos à utilizar métodos triviais para encriptar as mensagens. No estudo[[1]](#referencias), 51% dos usuários do grupo experimental entregaram códigos classificados como inseguros, contra 14% do grupo controle.

No entanto, além dos problemas presentes em código humano, os códigos gerados por IA estão sucetiveis à vulnerabilidades novas, como é a chamada "Alucinação de Pacotes", que se resume à invenção de dependências que não existem. Em estudo realizado com 2,23 milhões de pacotes gerados por IA, 19,7% eram alucinações[3](#referencias). Essa vulnerabilidade é explorável por usuários mal intencionados que podem criar e publicar os pacotes com arquivos maliciosos.

Também existe um fator psicológico semelhante à um Viés de Autoridade mas aplicado à IA. O estudo de Perry, N et al. aponta que, em todos os problemas propostos, quando os participantes foram questionados sobre a segurança do seu código, aqueles que utilizaram inteligência artificial estavam mais propensos à julgar o seu código como seguro quando não era, do que o grupo controle.

## Ponderações

IAs são modelos estatísticos de linguagem baseados na predição de tokens. Ou seja, dada uma entratada, o modelo estatisticamente, baseado nos pesos utilizados no treinamento, faz a previsão de qual é o próximo melhor token até concluir a resposta. O que é diferente da ponderação realizada por um humano. 

Os modelos de linguagem utilizados nos estudos referenciados já são defasados. Os mais recentes deles tendo sido lançados no final de 2023, como o GPT-4, e início de 2024, como Claude 3[3](#referencias).

Usuários que questionam e roformulam os prompts estão mais propensos a desenvolverem códigos mais seguros[[1]](#referencias). 

## Conclusão

Dado o cenário de estudos analisado, é categorica a definição de que o desenvolvimento de código utilizando os LLMs analisados gera mais vulnerabilidades do que quando feito por um humano qualificados. No entanto é indiscutível que o IA possibilita um desenvolvimento muito mais rápido, podendo ser uma ferramente muito poderosa desde que utilizada por alguém capacitado para revisar o código gerado.

Também é importante lembrar que essa é uma tecnologia emergente que passa por um processo acelerado de desenvolvimento.

## Referências

1. [Do Users Write More Insecure Code with AI Assistants?](https://dl.acm.org/doi/epdf/10.1145/3576915.3623157) - Neil Perry, Megha Srivastava, Deepak Kuma & Dan Boneh;

2. [Asleep at the Keyboard? Assessing the Security of GitHub Copilot’s Code Contributions](https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=9833571) - Pearce, H. et al.; ;

3. [We Have a Package for You! A Comprehensive Analysis of Package Hallucinations by Code Generating LLMs](https://arxiv.org/pdf/2406.10279) - Spracklen, J. et al.;

4. [The Rise of Slopsquatting: How AI Hallucinations Are Fueling a New Class of Supply Chain Attacks](https://socket.dev/blog/slopsquatting-how-ai-hallucinations-are-fueling-a-new-class-of-supply-chain-attacks) - Sarah Gooding, Socket.dev
