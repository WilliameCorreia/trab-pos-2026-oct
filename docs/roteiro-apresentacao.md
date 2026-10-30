# Roteiro da apresentação (31/10)

Não vamos rodar o sistema ao vivo. Os slides explicam o projeto inteiro e mostram, com capturas reais, cada parte funcionando. São **26 slides, cerca de 17 minutos**, em 4 partes, uma por pessoa. Dos 25 minutos do grupo, sobram cerca de 3 para perguntas, além dos 5 de feedback.

Cada slide tem, embaixo, uma **explicação curta em texto corrido**, legível pela plateia: se você se perder, leia esse texto e siga. Abaixo está, para cada slide, essa explicação (**Na tela**), o **passo a passo do que apontar** e a **fala completa**, a mesma das notas do apresentador. Não precisa decorar: entenda a ideia e fale do seu jeito.

| Quem | Parte | Slides | Tempo |
|---|---|---|---|
| **Apresentador 1:** _nome_ | Visão geral e arquitetura | 1 a 8 | 5 min 10 s |
| **Apresentador 2:** _nome_ | Serviço de blobs | 9 a 11 | 3 min 10 s |
| **Apresentador 3:** _nome_ | Serviço de fontes e integração | 12 a 18 | 4 min 55 s |
| **Apresentador 4:** _nome_ | Decisões, desempenho e evolução | 19 a 26 | 4 min 05 s |

## As duas imagens que todo mundo precisa conhecer

Qualquer pergunta sobre “como funciona” se responde com uma destas duas imagens (slides 5 e 7, e no README).

**Fluxo: do fonte ao executável.** Arquivos com revisões → workspace compila num `.o` (fila `compila`) → assemblagem linka num executável (fila `assembla`) → download. O blob-service guarda todos os bytes.

![Fluxo do fonte C ao executável](img/fluxo.png)

**Arquitetura: os 20 containers.** O caminho síncrono aparece em preto (bff → serviços, nginx na frente das réplicas) e o assíncrono em vermelho (RabbitMQ, workers e eventos). Cada serviço tem o próprio Postgres, e a observabilidade fica embaixo.

![Arquitetura com os 20 containers](img/arquitetura.png)

## Antes de começar

- [ ] Preencher os nomes na capa dos slides e no README.
- [ ] Cada um ler a sua parte deste roteiro e o glossário no final; ensaiar com cronômetro.
- [ ] Abrir os slides no modo apresentação, com as notas visíveis para quem fala.
- [ ] Ter o repositório aberto (GitHub ou VS Code), caso o professor peça para ver um arquivo: `docker-compose.yml`, um ADR ou `docs/slos.md`.

## O trabalho atende ao que o plano de ensino pede?

Conferido no repositório em 30/10. ✅ = pronto; ⏳ = só pode ser feito no dia ou depois.

| Exigência do plano | Onde está | Slides | Situação |
|---|---|---|---|
| Documento de arquitetura: contexto e problema | `docs/arquitetura.md`, seção 1 | 2, 3 | ✅ |
| SLOs definidos | `docs/arquitetura.md` seção 7 e `docs/slos.md` | 21 | ✅ |
| Estratégia de observabilidade | `docs/arquitetura.md` seção 8, ADR-009 | 17, 18 | ✅ |
| Estratégia de segurança | `docs/arquitetura.md` seção 9, ADR-008 | 24 | ✅ |
| Pelo menos 3 ADRs no template (contexto, alternativas, decisão, consequências) | `docs/ADR-001` a `012`: são 12, todos com as quatro seções | 19, 20 | ✅ |
| Pelo menos 1 ADR revisado com o feedback do peer review | ainda não existe | — | ⏳ depois da apresentação |
| Pelo menos 2 microsserviços com lógica de negócio | 5 serviços (bff, source, blob, workspace, assemblage) e o worker | 4, 6, 7 | ✅ |
| Endpoint de health check | `/health/live` e `/health/ready` nos 5 serviços e no worker | 17 | ✅ |
| Log estruturado com Correlation ID | logs JSON (pino) com o ID em todos; o ID viaja no HTTP e nas mensagens | 17, 18 | ✅ |
| `docker-compose.yml` sobe tudo com `docker compose up --build` | 20 containers; testado a partir de um clone vazio | 8 | ✅ (ao vivo, ver o fim deste roteiro) |
| Medição com p50, p95, p99 e throughput de pelo menos um endpoint, com conclusão | `docs/slos.md` e `docs/resultados-carga.md` | 21 a 23 | ✅ |
| Checklist de peer review preenchido | entregue ao professor no dia | — | ⏳ no dia |

**Como seremos avaliados** (pesos do plano) e onde cada critério aparece:

| Critério | Peso | Onde mostramos |
|---|---|---|
| Coerência arquitetural | 25 % | Slides 2 a 7 e 25; `docs/arquitetura.md` explica também os padrões que *não* usamos e por quê (Blue/Green, Strangler Fig, ACL, CDC) |
| Qualidade dos ADRs | 20 % | Slides 19 e 20; 12 ADRs, com histórico de decisões substituídas |
| Ambiente containerizado | 25 % | Slide 8 (saída real) e a seção sobre demonstração ao vivo, no fim |
| Resultado de medição | 15 % | Slides 21 a 23; protocolo e relatório em `docs/slos.md` |
| Health check e logging | 10 % | Slides 17 e 18 |
| Participação no peer review | 5 % | No dia: checklist do outro grupo e revisão de um ADR depois |

**Se o professor perguntar sobre um tema das aulas:**

| Tema | Onde respondemos |
|---|---|
| Comunicação síncrona × assíncrona, padrão Queue | Slides 6 e 14; ADR-003 e ADR-011 |
| Consistência forte × eventual, CAP e PACELC | `docs/arquitetura.md`, seção 6: forte dentro do serviço, eventual entre serviços |
| Latência percentílica (p50, p95, p99) | Slide 21 e glossário |
| Database Decomposition | Slide 20 (fase 2 → fase 3) |
| Rolling, Canary e Blue/Green | Slide 25; Blue/Green descartado por custo (`docs/arquitetura.md`, seção 10) |
| Strangler Fig, ACL e CDC | Não se aplicam: o sistema não veio de um legado (`docs/arquitetura.md`, seção 10) |
| JWT, OAuth, TLS, rate limit (OWASP API Top 10) | Slide 24: o que falta e o plano para cada item |
| Prometheus e alertas por SLO | Limitação declarada: hoje os SLOs são medidos por teste de carga, não continuamente |

**Ainda falta fazer:** colocar os nomes na capa e no README; preencher o checklist do outro grupo; revisar um ADR com o feedback recebido; fazer o merge na `main` e o push.

## Parte 1 · Visão geral e arquitetura (Apresentador 1)

### 1. Registro de Fontes (15 s)

**Como explicar** (aponte nesta ordem):

1. Diga o nome do grupo e o do projeto.
2. Leia o subtítulo: ele resume o sistema em uma linha.
3. Anuncie as 4 partes e quem apresenta cada uma.

**Fala:**

“Boa noite. Somos [nomes]. Nosso projeto se chama Registro de Fontes: um sistema para guardar código em C com histórico de versões, compilar esse código e gerar um programa executável, tudo dividido em microsserviços. A apresentação tem quatro partes: visão geral e arquitetura; o serviço de blobs; o serviço de fontes e como tudo se integra; e por fim as decisões e os números. Tudo o que vamos mostrar são capturas reais do sistema rodando.”

### 2. Três cargas com perfis opostos (40 s)

**Na tela:** O sistema faz três trabalhos diferentes: ler e salvar arquivos (leve e frequente), guardar bytes (volume, sem regra) e compilar (lento e arriscado). Separados, um pico de compilações não deixa a leitura lenta, e uma falha no compilador não derruba o resto.

**Como explicar** (aponte nesta ordem):

1. Leia a linha de cima: é o fluxo do sistema em uma frase.
2. Passe pelos três cartões da esquerda para a direita; a linha vermelha de cada um é a conclusão (escalar, reaproveitar, tirar da requisição).
3. Feche com a frase de baixo, sobre o monolito.

**Fala:**

“O sistema faz três coisas bem diferentes. Primeiro, ler e salvar arquivos e versões: é leve, acontece o tempo todo e precisa ser rápido. Segundo, guardar o conteúdo, os bytes: é volume, sem regra de negócio, e serve para o código, para o objeto compilado e para o executável. Terceiro, compilar e linkar: é lento, gasta CPU e roda código que qualquer pessoa enviou. Se tudo estivesse num programa só, um monolito, um pico de compilações deixaria até a leitura lenta, e um travamento do compilador derrubaria tudo. Separando, cada parte cresce e falha sozinha.”

*Termos:* monolito = um único programa com todas as funções juntas.

### 3. Por que microsserviços? Seis perguntas (45 s)

**Na tela:** Quatro respostas são sim, e são elas que justificam dividir o sistema. As outras duas mostram o que ainda falta: o trabalho em times foi simulado e não temos CI/CD nem métricas. O preço da divisão é ter mais containers, mais rede e mais coisa para monitorar.

**Como explicar** (aponte nesta ordem):

1. Leia as perguntas 1 a 4 (todas “SIM”) e dê o porquê de cada uma em uma frase.
2. Mostre as etiquetas diferentes: 5 é “SIMULADO” e 6 é “EM PARTE”.
3. Termine no texto de baixo: quatro sins justificam a divisão; o preço é mais coisa para operar.

**Fala:**

“Antes de dividir um sistema, vale responder a seis perguntas. Primeira: as partes têm necessidades diferentes? Sim: ler é leve, compilar é pesado. Segunda: uma falha pode ficar isolada? Sim: se o compilador travar, a edição continua. Terceira: dá para escalar só o que precisa? Sim: subimos mais workers de compilação com um comando. Quarta: as fronteiras são claras? Sim: fonte, objeto e executável são etapas naturais. As outras duas, com honestidade: vários times? Só simulamos, com quatro frentes. E estrutura para operar? Em parte: temos compose, health checks, logs e failover, mas não CI/CD nem métricas. Quatro sins justificam a divisão; o preço é ter mais containers, mais rede e mais coisa para monitorar.”

| # | Pergunta | Resposta | Por quê |
|---|---|---|---|
| 1 | As partes do sistema têm necessidades diferentes? | **Sim** | Ler e salvar é leve e constante; compilar é lento e gasta muita CPU. |
| 2 | Uma falha pode ficar isolada? | **Sim** | Se o compilador travar, abrir e editar arquivos continua funcionando. |
| 3 | Dá para escalar só a parte que precisa? | **Sim** | Mais workers de compilação com um comando, sem duplicar o resto. |
| 4 | As fronteiras do negócio são claras? | **Sim** | Fonte → objeto → executável: cada etapa usa o resultado da anterior. |
| 5 | Vários times trabalham ao mesmo tempo? | **Simulado** | Dividimos em 4 frentes com contratos de API; num time pequeno real, não. |
| 6 | Temos estrutura para operar tudo isso? | **Em parte** | Temos compose, health checks, logs e failover; faltam CI/CD e métricas. |

*Se perguntarem* “não seria melhor um monolito?”: para um time pequeno, um monolito modular seria uma escolha válida, e dizemos isso; as quatro primeiras respostas é que tornam a divisão natural aqui.

### 4. Bounded contexts (40 s)

**Na tela:** Cada caixa é uma parte do negócio com dono e vocabulário próprios. As três de cima são o coração do sistema; o armazenamento é genérico (poderia ser um S3); execução e interface dão apoio.

**Como explicar** (aponte nesta ordem):

1. Explique as etiquetas: CORE (o coração), GENERIC (poderia ser comprado), SUPPORTING (apoio).
2. Linha de cima: os três contextos centrais, na ordem do fluxo.
3. Em cada cartão, a última linha (fonte mono) é o serviço que implementa o contexto.

**Fala:**

“Bounded context é uma fronteira dentro da qual cada palavra tem um único significado e um único dono. Temos seis. Os três centrais, o que o sistema existe para fazer: Fontes, que guarda arquivos .c e .h com revisões; Workspaces, que junta fontes e compila num arquivo objeto; e Assemblagens, que junta objetos num executável. O genérico é o Armazenamento: só guarda bytes, e poderia até ser trocado por um S3. E dois de apoio: a Execução, os workers que rodam o gcc, e a Apresentação, a interface web.”

*Termos:* Core = o diferencial do sistema; Supporting = apoio; Generic = algo que se poderia comprar pronto.

### 5. Como funciona: do fonte ao executável (50 s)

**Na tela:** O usuário salva arquivos (cada salvamento vira uma revisão), junta alguns num workspace e pede para compilar: o pedido vai para uma fila e volta como um arquivo .o. Depois junta objetos numa assemblagem, que é linkada num executável para baixar.

**Como explicar** (aponte nesta ordem):

1. Siga os quadros numerados 1 → 4 no topo da imagem.
2. Aponte as setas vermelhas: o pedido desce para a fila e o evento sobe de volta.
3. Mostre a faixa cinza de baixo: todo byte vai para o blob-service.
4. A coluna da direita repete os 4 passos em texto, se precisar.

**Fala:**

“Vamos seguir um exemplo: uma calculadora com três arquivos, calc.h, calc.c e app.c. Passo 1: o usuário cadastra os arquivos; cada vez que salva, nasce uma revisão nova. Passo 2: ele cria um workspace, que é um conjunto de arquivos, cada um numa revisão fixa, e pede para compilar. O pedido vai para uma fila, um worker roda o gcc e devolve um arquivo objeto, o .o. Passo 3: ele cria uma assemblagem, juntando os objetos de dois workspaces, e o pedido vai para outra fila, onde outro worker linka tudo. Passo 4: ele baixa o executável e roda no próprio computador. Embaixo, o blob-service guarda todos os bytes.”

*Termos:* arquivo objeto (.o) = código de máquina de um pedaço do programa, ainda não executável; linkar = juntar os objetos num programa só.

### 6. Arquitetura implementada (40 s)

**Na tela:** Na linha de cima, o navegador fala só com o BFF, que chama os serviços diretamente. Na de baixo, compilar e linkar viram pedidos numa fila, atendidos pelos workers. O que está em vermelho tem réplicas.

**Como explicar** (aponte nesta ordem):

1. Linha de cima: caminho síncrono (navegador → bff → serviços).
2. Linha de baixo: workspace e assemblage, RabbitMQ e workers.
3. Caixas com borda vermelha têm réplicas; a linha tracejada é o caminho assíncrono.

**Fala:**

“Esta é a mesma ideia em caixas. Na linha de cima, o caminho síncrono: o navegador só conversa com o BFF, e o BFF chama os serviços diretamente por HTTP. Source e blob têm um balanceador nginx na frente e duas réplicas cada. Na linha de baixo, o assíncrono: workspace e assemblage não compilam nada, só colocam o pedido no RabbitMQ; os workers pegam o pedido, trabalham e devolvem um evento. O que está em vermelho tem réplicas e escala.”

*Termos:* BFF = backend for frontend, o único ponto que o navegador acessa; réplica = cópia idêntica do serviço rodando em paralelo; balanceador = distribui as requisições entre as réplicas.

### 7. Arquitetura detalhada: 20 containers (45 s)

**Na tela:** São 20 containers. Cada serviço tem o próprio banco e só acessa dados dos outros pela API. O RabbitMQ guarda os pedidos e os eventos de volta; o nginx distribui o tráfego entre as réplicas.

**Como explicar** (aponte nesta ordem):

1. Comece pela esquerda: navegador → bff → barramento vertical para os 3 serviços.
2. Aponte os cilindros: cada serviço tem o próprio Postgres.
3. Centro, em vermelho: o RabbitMQ e as 5 filas; à direita, os workers e a coluna do blob.
4. Feche na faixa de baixo (observabilidade) e no quadro “Resiliência” à esquerda.

**Fala:**

“Aqui estão os 20 containers. Da esquerda para a direita: navegador, BFF e os três serviços de negócio. Cada serviço tem o próprio Postgres, os cilindros: um serviço nunca lê o banco do outro, só pede pela API. No meio, em vermelho, o RabbitMQ com cinco filas: duas de pedido, compila e assembla; duas de resultado, os eventos que voltam; e a DLQ, para onde vão as mensagens que falharam. À direita, os workers e o blob. Embaixo, a observabilidade: alloy coleta os logs, loki guarda, grafana mostra.”

*Se perguntarem* “por que um banco por serviço?”: para que um serviço possa mudar suas tabelas sem quebrar os outros; isso está no ADR-006.

### 8. Um comando sobe tudo (35 s)

**Na tela:** O comando docker compose up --build constrói e sobe tudo. Cada serviço só inicia depois que o seu banco e as suas dependências passam no teste de saúde. Testamos a partir de um clone vazio do repositório.

**Como explicar** (aponte nesta ordem):

1. Mostre que a lista tem os 20 containers, em duas colunas.
2. Verde = “healthy”: o teste de saúde passou. Os sem marca (nginx, loki, grafana, alloy) não têm teste próprio.
3. Leia os três tópicos da direita: o comando, a ordem garantida e o teste a partir do clone.

**Fala:**

“Um critério importante do trabalho é o ambiente subir com um comando: docker compose up --build. A tela mostra a saída real do docker compose ps: os 20 containers de pé, e os que têm teste de saúde aparecem como healthy. A ordem é automática: o compose só sobe um serviço depois que o banco e as dependências dele estão prontos. Para ter certeza, clonamos o repositório numa pasta vazia e subimos do zero: funcionou, e o fluxo completo rodou.”

*Termos:* container = um processo isolado com tudo de que precisa para rodar; healthcheck = teste que o Docker faz periodicamente para saber se o serviço está pronto.

**Passagem para a próxima parte:** “O serviço que todos os outros usam é o de blobs. Com vocês, _nome do Apresentador 2_.”

## Parte 2 · Serviço de blobs (Apresentador 2)

### 9. Serviço de blobs: guarda bytes, não sabe o quê (1 min 10 s)

**Na tela:** Funciona como um guarda-volumes: recebe bytes, devolve um ticket (UUID) e nunca abre a mala. Por isso guarda código, objeto e executável sem mudar nada. O banco calcula um md5 para conferir que o conteúdo não mudou.

**Como explicar** (aponte nesta ordem):

1. Cartão da esquerda: as 4 rotas da API, todas simples.
2. Cartão da direita: quem grava e o que grava (código, .o, executável).
3. Linha de baixo: onde o dado fica e por que o BFF nunca vê o blob_id.

**Fala:**

“O blob-service funciona como um guarda-volumes: você entrega os bytes e recebe um ticket, o UUID. Quem guarda não abre a mala: ele não sabe se é código, objeto ou executável. Por isso os três tipos usam o mesmo serviço, sem nenhuma mudança nele; essa é a decisão do ADR-001. A API tem só quatro operações: gravar, que devolve o ID; ler os dados em JSON; ler os bytes crus; e o health check. Para cada blob, o Postgres calcula um md5, uma impressão digital para conferir que o conteúdo não mudou. Quem grava são o source-service e os dois workers; cada um guarda o ID no próprio banco.”

*Termos:* UUID = identificador único gerado aleatoriamente; BYTEA = tipo de coluna do Postgres para dados binários.

### 10. Blob: réplicas e falhas (1 min 00 s)

**Na tela:** Com duas réplicas, se uma cai a outra atende. Se o blob cair inteiro, quem chama desiste em 2 segundos e devolve erro 502, em vez de ficar travado ou fingir sucesso. Arquivos grandes são o limite conhecido.

**Como explicar** (aponte nesta ordem):

1. Leia os três números grandes, da esquerda para a direita: 2 réplicas, 20 de 20, 502 em 1 s.
2. Explique o 502: erro rápido e claro, melhor que travar ou fingir sucesso.
3. Termine no quadro cinza: o limite conhecido (arquivos grandes) e o próximo passo.

**Fala:**

“O blob tem duas réplicas atrás de um nginx. O nginx pergunta ao Docker a cada 5 segundos quais réplicas existem; então, se subimos uma nova, ela entra no revezamento sozinha. Testamos: com uma réplica do blob e uma do source derrubadas, 20 de 20 leituras funcionaram. E se o blob cair inteiro? A leitura responde erro 502 em 1 segundo, porque toda chamada entre serviços tem tempo limite de 2 segundos. Antes, respondia 200 com o corpo vazio, o que é pior: parecia sucesso. Esse bug apareceu na análise do grupo. Limite assumido: guardar no Postgres não serve para arquivos grandes; o próximo passo seria um MinIO ou S3, sem mudar os outros serviços.”

*Termos:* 502 = erro de gateway, ou seja, o serviço de trás falhou; timeout = tempo máximo de espera.

### 11. Na prática: uma réplica cai, o serviço continua (1 min 00 s)

**Na tela:** Saída real do terminal: o nginx alterna entre as duas réplicas; derrubamos uma e as leituras continuaram dando 200; religamos e, cerca de 20 segundos depois, ela voltou ao revezamento sozinha.

**Como explicar** (aponte nesta ordem):

1. Primeiro bloco: os finais .11 e .10 se alternam → duas réplicas no rodízio.
2. docker stop → as quatro linhas “200” em verde: nenhuma falha.
3. docker start → último bloco: .11 e .10 alternando de novo, cerca de 20 s depois.

**Fala:**

“Esta é a saída real do terminal. Nas quatro primeiras linhas, o cabeçalho X-Upstream mostra qual réplica respondeu: o final .11 e o .10 se alternam, porque o nginx faz rodízio. Depois, paramos a réplica 1 com docker stop. As quatro leituras seguintes deram 200: o nginx percebeu e mandou tudo para a outra. Por fim, religamos com docker start e, cerca de 20 segundos depois, ela voltou ao rodízio sozinha, sem reiniciar nada.”

*Se perguntarem* “e se as duas caírem?”: aí a leitura falha com 502 em 1 segundo, como no slide anterior; para mais disponibilidade, bastaria subir mais réplicas.

**Passagem para a próxima parte:** “O blob guarda os bytes; quem dá sentido a eles é o serviço de fontes. _Nome do Apresentador 3_.”

## Parte 3 · Serviço de fontes e integração (Apresentador 3)

### 12. Serviço de fontes: revisões imutáveis (45 s)

**Na tela:** Salvar nunca apaga a versão anterior: sempre cria a próxima revisão. Se duas pessoas salvam ao mesmo tempo, o banco numera uma de cada vez. É o serviço mais usado, por isso também tem duas réplicas.

**Como explicar** (aponte nesta ordem):

1. Cartão “Modelo”: um registro por arquivo, com a lista de revisões.
2. Explique a REV n+1 calculada no banco: sem duas revisões com o mesmo número.
3. Cartão “API”: criar, nova revisão, ler conteúdo, listar revisões.

**Fala:**

“O source-service guarda o nome do arquivo e a lista de revisões; o conteúdo mesmo fica no blob. A regra principal: revisões são imutáveis. Salvar nunca sobrescreve; sempre cria a REV seguinte. E se duas pessoas salvarem ao mesmo tempo? O número é calculado dentro do próprio comando do banco, que trava aquela linha por um instante, então nunca saem duas REV 3. É o serviço mais usado: a interface lê e grava aqui, o workspace-service confere se a fonte existe, e o worker baixa o código daqui. Por isso tem duas réplicas.”

### 13. Tela: um arquivo e suas revisões (35 s)

**Na tela:** A interface real: o calc.c está na revisão 2 e a revisão 1 continua disponível. Ao lado, as revisões e os workspaces que usam este arquivo.

**Como explicar** (aponte nesta ordem):

1. À esquerda, a lista de arquivos; o triângulo vermelho marca o aberto.
2. No centro, o código do calc.c na REV 2.
3. À direita, as revisões (REV 2 atual, REV 1 guardada) e os workspaces que usam o arquivo.

**Fala:**

“Esta é a interface real. O arquivo calc.c está na REV 2: alteramos uma linha, salvamos com Ctrl+S e o sistema criou a revisão 2, sem apagar a 1. À direita estão as revisões, com a atual marcada, e os workspaces que usam este arquivo e em qual revisão. Embaixo, o carimbo mostra os dados do arquivo e o Correlation ID da requisição, que vamos explicar adiante.”

### 14. Compilar e linkar: fila e evento de volta (45 s)

**Na tela:** Compilar demora, então a tela não espera: o serviço responde “recebi” (202) e coloca o pedido na fila. Um worker compila, guarda o .o e avisa com um evento. Se algo falha, a mensagem vai para uma fila de erros.

**Como explicar** (aponte nesta ordem):

1. Leia os 5 passos numerados, da esquerda para a direita.
2. Destaque o passo 2: “202 Accepted” = recebi, vou fazer depois.
3. Cartões de baixo: a assemblagem segue o mesmo padrão; erros vão para a DLQ.

**Fala:**

“Por que fila? Compilar demora e gasta CPU; se a tela esperasse, ficaria travada. Então: um, o workspace está pronto; dois, o serviço responde 202, que significa ‘recebi, vou fazer’, e coloca o pedido na fila compila; três, um worker pega o pedido e roda o gcc; quatro, grava o .o no blob; cinco, publica o evento ‘compilei’, e o workspace-service atualiza o estado. A assemblagem segue o mesmo padrão com a fila assembla. Se algo dá errado, a mensagem vai para a DLQ, uma fila de erros, sem travar a fila principal.”

*Termos:* idempotente = se o mesmo evento chegar duas vezes, o resultado é o mesmo de uma vez só, o que importa porque a fila garante entrega ‘pelo menos uma vez’.

### 15. Tela: workspace compilado e workspace com erro (50 s)

**Na tela:** À esquerda, compilado: duas fontes em revisões fixas viraram um .o de 1,3 kB, e a tela atualizou sozinha. À direita, um erro proposital: a mensagem do gcc aparece com a linha do problema.

**Como explicar** (aponte nesta ordem):

1. Imagem da esquerda: as duas fontes com a revisão fixada e o objeto de 1,3 kB.
2. Imagem da direita (borda vermelha): o quadro vermelho com a mensagem do gcc e a linha do erro.
3. Leia as legendas embaixo de cada imagem.

**Fala:**

“À esquerda, o workspace calc-lib: tem calc.c na revisão 2 e calc.h na revisão 1, fixadas. Clicamos em Compilar: o estado foi de ‘na fila’ para ‘compilado’ sem recarregar a página, porque a tela consulta o estado a cada segundo. O resultado é um arquivo objeto de 1,3 kB, que pode ser baixado. À direita, um workspace com erro de propósito: falta um ponto e vírgula, e a mensagem do gcc aparece na tela, apontando a linha. Para corrigir, salva-se uma nova revisão e atualiza-se o workspace.”

### 16. Tela: assemblagem e o executável rodando (40 s)

**Na tela:** A assemblagem junta os objetos dos dois workspaces num executável de 137 kB, que roda em qualquer Linux. Baixamos e rodamos no terminal. Por segurança, o programa nunca é executado no servidor.

**Como explicar** (aponte nesta ordem):

1. Na tela, os dois objetos usados e o botão vermelho “Baixar executável”.
2. No terminal: chmod, ./calculadora e o resultado 42 e 49.
3. Feche nos dois tópicos: linkado estático e nunca roda no servidor.

**Fala:**

“A assemblagem calculadora junta os objetos dos dois workspaces. Ela guarda uma cópia deles no momento do pedido; se alguém recompilar um workspace depois, esta assemblagem não muda. O worker linka com gcc -static, ou seja, coloca tudo de que o programa precisa dentro dele: são 137 kB, e ele roda em qualquer Linux. Baixamos e rodamos no terminal: dobro de 21 dá 42 e quadrado de 7 dá 49. Por segurança, o programa nunca é executado no servidor.”

### 17. Um ID segue o pedido inteiro (40 s)

**Na tela:** O Correlation ID é como um número de protocolo: nasce no primeiro clique e vai junto em cada chamada e em cada mensagem da fila. Todos os logs carregam esse número, então dá para seguir um pedido inteiro.

**Como explicar** (aponte nesta ordem):

1. Siga a corrente de etiquetas no topo: é o caminho do mesmo ID.
2. Cartões: logs centralizados, health checks e os dois bugs encontrados.
3. Anuncie o próximo slide: a captura real do Grafana.

**Fala:**

“Com o pedido passando por tantos serviços, como achar um problema? O Correlation ID funciona como um número de protocolo: nasce no BFF e é repassado em cada chamada HTTP e dentro de cada mensagem da fila, na ida e na volta. Todo serviço escreve logs em JSON com esse ID. O Alloy coleta os logs de todos os containers, o Loki guarda e o Grafana mostra. Também temos health checks: /health/live diz se o processo está de pé; /health/ready diz se as dependências respondem. Os logs nos ajudaram a achar dois bugs: uma rota que devolvia erro em toda compilação e um serviço que caía quando o banco reiniciava.”

### 18. Na prática: uma compilação, 16 linhas, 6 componentes (40 s)

**Na tela:** Tela real do Grafana filtrada pelo ID de uma compilação: do pedido na fila até o objeto vinculado, passando pelo worker, pelo source e pelo blob. São 16 linhas de 6 componentes, em 26 ms.

**Como explicar** (aponte nesta ordem):

1. No canto de cima, à esquerda, o campo “Correlation ID” com o ID filtrado.
2. No gráfico, cada cor é um serviço.
3. Leia os logs de baixo para cima: publicação na fila → worker → source e blob → objeto vinculado.

**Fala:**

“Esta é a tela real do Grafana, filtrada pelo ID de uma compilação. Lendo de baixo para cima: o workspace-service publica na fila; o compila-worker recebe; busca os dois arquivos no source-service, que lê o conteúdo no blob; grava o .o no blob; publica o evento; e o workspace-service vincula o objeto. São 16 linhas, de 6 componentes, em 26 milissegundos. Sem o ID, teríamos que procurar em seis lugares diferentes.”

**Passagem para a próxima parte:** “Por fim, as decisões que nos trouxeram até aqui e os números. _Nome do Apresentador 4_.”

## Parte 4 · Decisões, desempenho e evolução (Apresentador 4)

### 19. Doze decisões registradas (30 s)

**Na tela:** Cada decisão importante virou um documento curto: o contexto, as alternativas, o que decidimos e as consequências. Quando mudamos de ideia, a decisão antiga não é apagada: fica marcada como substituída.

**Como explicar** (aponte nesta ordem):

1. Explique as colunas: número, decisão e status.
2. Em vermelho, os dois substituídos (004 → 011 e 005 → 006).
3. Cite os três destaques: 001, 008 e 011.

**Fala:**

“ADR significa Architecture Decision Record, um registro de decisão de arquitetura. Escrevemos 12, todos no modelo da aula: contexto, alternativas consideradas, decisão e consequências. Ficam na pasta docs, versionados junto com o código. O status mostra o ciclo de vida: Accepted é a decisão em vigor; Superseded é a que foi substituída por outra. Destaques: o 001, de separar os bytes num serviço próprio; o 008, de isolar os workers que compilam código de terceiros; e o 011, que refez a compilação seguindo a proposta original do grupo.”

### 20. Uma decisão que mudou: ADR-005 → ADR-006 (25 s)

**Na tela:** Começamos com um banco só, separado por schemas, e descobrimos que na prática não havia separação. Corrigimos para um banco por serviço e registramos a mudança: quem lê entende o porquê.

**Como explicar** (aponte nesta ordem):

1. Cartão da esquerda: a decisão antiga e o que descobrimos.
2. Bloco vermelho no meio = a mudança.
3. Cartão da direita: a decisão atual. A frase de baixo explica o “Superseded”.

**Fala:**

“Um exemplo de como um ADR evolui. No começo, a decisão 005 era um Postgres só, com um schema por serviço. Ao implementar, descobrimos que as tabelas estavam todas no mesmo schema, o public: na prática, não havia separação nenhuma. Corrigimos e demos um passo além: um banco por serviço, que é o ADR-006. O 005 não foi apagado: virou Superseded, e quem ler entende por que a decisão mudou.”

*Termos:* schema = uma pasta de tabelas dentro do banco.

### 21. Desempenho: SLOs medidos com k6 (35 s)

**Na tela:** Antes de medir, definimos metas (SLOs). p95 de 1,64 ms quer dizer que 95 % das consultas foram mais rápidas que isso. Todas as metas foram atendidas; como o teste foi local, não as apertamos.

**Como explicar** (aponte nesta ordem):

1. Explique as colunas: SLI (o que medimos), meta, medido, atende.
2. Primeira linha: o protocolo do grupo (6.000 de 6.000; p95 de 1,64 ms).
3. Leia a ressalva da linha de baixo: teste local, metas mantidas.

**Fala:**

“Antes de medir, definimos metas. SLI é o que medimos, por exemplo o tempo de resposta; SLO é a meta, por exemplo 95 % das respostas abaixo de 300 ms. Os percentis: p95 de 1,64 ms quer dizer que 95 % das requisições foram mais rápidas que isso. No protocolo que o grupo escreveu, 6.000 consultas em 5 minutos: todas válidas, com p50 de 1,33, p95 de 1,64 e p99 de 1,81 milissegundos, contra metas de 100, 300 e 800. Ressalva honesta: o teste rodou na mesma máquina, sem rede real, então não apertamos as metas.”

### 22. Carga e escala (25 s)

**Na tela:** Testamos até 800 requisições por segundo, 40 vezes o pico estimado, sem nenhum erro. Com dois workers, o RabbitMQ dividiu as compilações meio a meio, sem configurar nada.

**Como explicar** (aponte nesta ordem):

1. Leia os quatro números grandes: 800 req/s, 99,6 %, 4 + 4 e 28 ms.
2. Uma frase para cada um, usando a legenda embaixo do número.

**Fala:**

“No teste de estresse, só leitura, subimos até 800 requisições por segundo, 40 vezes o pico que estimamos, sem nenhum erro. Com dois workers de compilação, 8 pedidos foram divididos 4 e 4 pelo RabbitMQ. Disponibilidade composta: quando um pedido passa por vários componentes, as chances de falha se somam; com réplicas, a leitura chega a 99,6 % na conta teórica. E a compilação leva 28 ms no p95, do pedido ao evento de volta.”

### 23. Na prática: escalar workers e medir (45 s)

**Na tela:** Um comando sobe mais workers, e o RabbitMQ passa a dividir a fila entre eles, sem mudar código. À direita, o teste de carga misto: todos os limites atendidos, 3.809 requisições e nenhuma falha.

**Como explicar** (aponte nesta ordem):

1. Terminal da esquerda: o comando --scale e as 3 linhas de container.
2. A última linha em verde: “consumers”: 3.
3. Terminal da direita: cada “✓” é um limite atendido; termine em 0 falhas e 3.809 requisições.

**Fala:**

“À esquerda, saída real: um único comando, docker compose up com --scale compila-worker=3, criou mais dois workers. Logo abaixo, a API do RabbitMQ confirma: a fila compila agora tem 3 consumidores. Não mudamos nenhuma linha de código: o worker não guarda estado, então qualquer um pode pegar qualquer pedido. À direita, o teste misto do k6, com leitura, página e compilação ao mesmo tempo: todos os limites passaram, com 3.809 requisições e zero falhas.”

*Termos:* k6 = ferramenta de teste de carga que simula muitos usuários.

### 24. Segurança: o que fazemos e o que falta (30 s)

**Na tela:** O risco principal é compilar código de qualquer pessoa. Por isso o gcc roda isolado, com tempo e recursos limitados, e o programa nunca é executado. Login e limite por usuário ainda faltam, e estão planejados.

**Como explicar** (aponte nesta ordem):

1. Coluna da esquerda: o que já está implementado; comece pelo “nunca é executado”.
2. Coluna da direita, em vermelho: o que falta e o plano de cada item.
3. Cite o OWASP API Top 10, a referência das aulas.

**Fala:**

“O maior risco deste sistema é que ele compila código enviado por qualquer pessoa. O que fazemos: o programa compilado nunca é executado no servidor; o gcc roda sem shell, com tempo limite de 10 segundos, sem as variáveis de ambiente do worker, e num container com limite de memória, CPU e processos; e o nome do arquivo é limpo, para não virar uma opção do gcc. O que falta, e está documentado com plano, ligado ao OWASP API Top 10: login, limite de requisições por usuário e criptografia entre os serviços.”

### 25. Deploy, evolução e próximos passos (30 s)

**Na tela:** Como os serviços não guardam estado e têm teste de saúde, dá para atualizar trocando uma instância por vez, sem parar o sistema. O que falta para produção (réplicas em todos, métricas, CI/CD) está listado.

**Como explicar** (aponte nesta ordem):

1. Três cartões, da esquerda para a direita: rolling update, canary, expand/contract.
2. Linha “Próximos passos”: o que faríamos para produção.
3. Linha “Migração”: por que o Strangler Fig não se aplica.

**Fala:**

“Como atualizar sem parar o sistema? Rolling update: sobe a versão nova, espera o health check dizer que está pronta e só então tira a antiga; isso já é possível porque os serviços não guardam estado. Canary: manda só uma parte do tráfego para a versão nova e observa os erros no Grafana; faria sentido no BFF. Expand/contract: mudanças no banco e nas mensagens só acrescentam campos, para a versão velha e a nova conviverem. O Strangler Fig, de substituir um monolito aos poucos, não se aplica: o sistema já nasceu dividido.”

### 26. O que está no repositório (25 s)

**Como explicar** (aponte nesta ordem):

1. Leia as cinco entregas e onde cada uma está.
2. Agradeça e abra para perguntas.

**Fala:**

“Para fechar: tudo o que o plano pede está no repositório. O documento de arquitetura, em docs/arquitetura.md; os 12 ADRs; seis serviços e dois workers, todos com health check e logs com Correlation ID; o docker-compose.yml, que sobe os 20 containers e foi testado do zero; e as medições, com p50, p95 e p99. Obrigado. Ficamos à disposição para perguntas.”

## Glossário

| Termo | Em linguagem simples |
|---|---|
| Microsserviço | Uma parte do sistema que roda como um programa separado, com seu próprio banco, e conversa com as outras pela rede. |
| Monolito | O contrário: um único programa com todas as funções. |
| Bounded context | Uma fronteira do negócio onde cada palavra tem um só significado e um só dono (por exemplo, "revisão" só existe em Fontes). |
| BFF (backend for frontend) | O único serviço que o navegador acessa; ele monta as telas e chama os outros serviços. |
| Blob | Um pedaço de bytes guardado sem que o serviço saiba o que é (texto, `.o` ou executável). |
| UUID | Um identificador único e aleatório, usado como "ticket" de cada blob, arquivo ou workspace. |
| Revisão (REV) | Uma versão salva de um arquivo; nunca é alterada depois de criada. |
| Workspace | Um conjunto de arquivos, cada um numa revisão fixa, que é compilado num arquivo objeto. |
| Arquivo objeto (`.o`) | O código de máquina de uma parte do programa; ainda não roda sozinho. |
| Assemblagem / linkar | Juntar vários `.o` num executável. Usamos `gcc -static`, que coloca tudo dentro do programa. |
| Réplica | Uma cópia idêntica de um serviço rodando ao mesmo tempo, para dividir carga e sobreviver a falhas. |
| Load balancer (nginx) | Distribui as requisições entre as réplicas e desvia das que caíram. |
| Fila (RabbitMQ) | Uma lista de pedidos de trabalho: quem pede não espera; um worker pega quando puder. |
| Worker | Um processo que só consome uma fila e faz o trabalho pesado (aqui, rodar o gcc). |
| Evento | Mensagem que o worker publica quando termina ("compilei"), para avisar o serviço dono do pedido. |
| 202 Accepted | Resposta HTTP que significa "recebi, vou fazer depois". |
| DLQ (dead letter queue) | Fila para onde vão as mensagens que falharam, para análise, sem travar as outras. |
| Idempotente | Fazer duas vezes dá o mesmo resultado que fazer uma; importante porque a fila pode entregar repetido. |
| Timeout | Tempo máximo de espera; passou disso, o serviço desiste e devolve erro. |
| 502 Bad Gateway | Erro que significa "o serviço de trás falhou". |
| Healthcheck (`/health/live`, `/health/ready`) | Teste de saúde: *live* diz se o processo está de pé; *ready* diz se as dependências (banco, fila) respondem. |
| Correlation ID | Um "número de protocolo" que acompanha o pedido por todos os serviços e aparece em todos os logs. |
| Loki / Alloy / Grafana | Alloy coleta os logs dos containers, Loki guarda, Grafana mostra e filtra. |
| Docker Compose | Ferramenta que sobe todos os containers com um comando, na ordem certa. |
| ADR | Architecture Decision Record: um documento curto que registra uma decisão, as alternativas e as consequências. |
| Accepted / Superseded | Status de um ADR: em vigor / substituído por outro (o antigo não é apagado). |
| SLI / SLO | SLI é o que se mede (ex.: tempo de resposta); SLO é a meta (ex.: 95 % abaixo de 300 ms). |
| p50 / p95 / p99 | Percentis: p95 = 1,64 ms quer dizer que 95 % das requisições foram mais rápidas que 1,64 ms. |
| k6 | Ferramenta que simula muitos usuários fazendo requisições, para medir desempenho. |
| Rolling update / Canary | Atualizar trocando uma instância de cada vez / mandar só parte do tráfego para a versão nova. |
| Strangler Fig | Substituir um sistema antigo aos poucos; não se aplica aqui, porque o sistema já nasceu dividido. |

## Perguntas prováveis

**Por que não um monolito?** Para um time pequeno, um monolito modular seria uma escolha válida, e dizemos isso no slide 3. Aqui, as três cargas têm perfis opostos e o domínio tem fronteiras naturais (fonte → objeto → executável); o trabalho também pedia microsserviços.

**Por que fila para compilar, e não uma chamada direta?** Compilar é lento e gasta CPU. Com a fila, a tela responde na hora (202), os pedidos não se perdem se o worker cair (ficam na fila) e para aguentar mais basta subir mais workers.

**O que acontece se o worker cair no meio de uma compilação?** A mensagem só é confirmada depois de terminar. Se o worker morre antes, o RabbitMQ devolve a mensagem para a fila e outro worker pega.

**E se o RabbitMQ cair?** As filas são duráveis e as mensagens persistentes: ao voltar, os pedidos continuam lá. Os serviços se reconectam sozinhos. Ele é um ponto único de falha, e isso está documentado.

**Por que um banco por serviço?** Para que cada serviço possa mudar suas tabelas sem quebrar os outros. Começamos com um banco só (ADR-005) e mudamos (ADR-006) quando vimos que não havia separação de fato.

**Por que guardar os arquivos no Postgres, e não num S3?** Simplicidade para o trabalho. O limite está assumido no ADR-010: arquivos grandes passariam inteiros pela memória. Como todos acessam o blob pela API, trocar por MinIO ou S3 não muda os outros serviços.

**Compilar código de qualquer pessoa não é perigoso?** É o maior risco, e por isso o programa nunca é executado no servidor; o gcc roda sem shell, com tempo limite, sem variáveis de ambiente e num container com limite de memória, CPU e processos (ADR-008). Falta autenticação e limite por usuário, e isso está no plano.

**Os números não são bons demais?** São números de teste local, sem rede entre as máquinas e com arquivos pequenos. Por isso não apertamos as metas: elas valem para um ambiente real.

**Como vocês sabem que o docker compose funciona?** Clonamos o repositório numa pasta vazia, subimos do zero e rodamos o fluxo completo. A saída real está no slide 8.

## Feedback e peer review (5 min)

- **Apresentador 4** anota o feedback do professor e do grupo parceiro. Depois da aula, pelo menos um ADR precisa ser revisado com esse feedback (é critério de avaliação: 5 %).
- **Enquanto o outro grupo apresenta**, _nome_ preenche o checklist de peer review e entrega ao professor ao final da apresentação deles.

## Se perguntarem "por que não rodaram ao vivo?"

O plano de ensino prevê a demonstração ao vivo do Docker Compose. Os slides 8, 11, 16, 18 e 23 trazem saídas reais do ambiente rodando. Se o professor quiser ver ao vivo, um notebook com Docker sobe tudo em cerca de 35 s (com as imagens já baixadas):

```bash
docker compose up --build -d
docker compose ps
```

Depois é só abrir http://localhost:3000. O passo a passo completo está no README.
