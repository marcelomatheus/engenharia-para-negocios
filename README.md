<div align="center">

# Engenharia de Software para negócios

### Engenharia de software aplicada a problemas reais

Arquitetura, confiabilidade, consistência, mensageria e modelagem de domínios como **financeiro**, **comercial**, **e-commerce** e **plataformas SaaS**.

</div>

---

> [!NOTE]
> Este não é um catálogo de padrões isolados.
>
> Cada conteúdo parte de um problema que poderia acontecer em produção: pagamentos duplicados, pedidos perdidos, estoque negativo, eventos fora de ordem, webhooks repetidos, concorrência entre processos ou regras comerciais difíceis de manter.

## Sobre o repositório

Muitos conteúdos de engenharia de software explicam conceitos usando exemplos excessivamente simples:

* um CRUD de usuários;
* uma API de tarefas;
* uma tabela de produtos;
* um endpoint sem concorrência;
* uma aplicação que nunca falha.

Esses exemplos são úteis para aprender sintaxe, mas não representam completamente os desafios encontrados em sistemas reais.

Na prática, engenheiros precisam responder perguntas como:

* O que acontece quando o cliente envia a mesma requisição duas vezes?
* Como impedir que um pagamento seja processado novamente?
* Como publicar um evento sem perder a alteração feita no banco?
* Como lidar com uma mensagem que sempre provoca erro?
* Como evitar a venda de um produto sem estoque?
* Como reconstruir o histórico de uma transação financeira?
* Como compensar uma operação distribuída que falhou pela metade?
* Como descobrir por que um pedido desapareceu entre dois serviços?

Este repositório explora essas situações por meio de **explicações, diagramas, decisões arquiteturais, testes e exemplos executáveis**.

---

## Objetivos

O projeto busca:

1. Demonstrar padrões de engenharia em cenários próximos da produção.
2. Relacionar decisões técnicas com regras de negócio reais.
3. Mostrar não apenas como implementar, mas quando utilizar cada solução.
4. Explicar os custos e limitações de cada abordagem.
5. Criar exemplos que possam ser executados, modificados e testados.
6. Aproximar conceitos de arquitetura da rotina de desenvolvedores.
7. Incentivar discussões sobre confiabilidade, consistência e operação.

---

## Princípios do projeto

### O problema vem antes do padrão

Nenhum exemplo começará com:

> “Vamos implementar Event Sourcing.”

O ponto de partida será:

> “Precisamos reconstruir cada mudança de estado de uma transação, preservar o histórico e explicar como o saldo chegou ao valor atual.”

Somente depois o padrão será apresentado como uma possível solução.

### Todo padrão possui custos

As implementações deverão discutir:

* complexidade operacional;
* impacto no banco de dados;
* dificuldade de manutenção;
* observabilidade;
* curva de aprendizado;
* comportamento em caso de falha;
* alternativas mais simples.

### Produção não é o caminho feliz

Os exemplos considerarão situações como:

* requisições repetidas;
* concorrência;
* timeout;
* mensagens duplicadas;
* eventos fora de ordem;
* indisponibilidade temporária;
* falhas parciais;
* reprocessamento;
* inconsistência de dados.

### O domínio direciona a arquitetura

Um checkout, um ledger financeiro e um pipeline comercial possuem necessidades diferentes.

A arquitetura deve refletir:

* regras de negócio;
* criticidade dos dados;
* volume de operações;
* necessidade de auditoria;
* tolerância à inconsistência;
* requisitos de disponibilidade.

---

## Estrutura de cada conteúdo

Cada tópico poderá seguir esta estrutura:

```text
1. Contexto de negócio
2. Problema
3. Exemplo de falha
4. Requisitos da solução
5. Conceito ou padrão
6. Diagrama da arquitetura
7. Implementação
8. Testes
9. Observabilidade
10. Limitações e alternativas
```

Um conteúdo sobre idempotência, por exemplo, não mostrará apenas um middleware. Ele deverá explicar:

* por que a duplicidade acontece;
* quais operações precisam ser idempotentes;
* como gerar e armazenar a chave;
* o que devolver em uma repetição;
* como lidar com requisições concorrentes;
* por quanto tempo manter o registro;
* quais métricas acompanhar.

---

## Estrutura proposta do repositório

```text
.
├── README.md
│
├── examples/
│   ├── architecture/
│   ├── reliability/
│   ├── messaging/
│   ├── financial/
│   ├── ecommerce/
│   ├── commercial/
│   └── platform/
│
├── shared/
│   ├── database/
│   ├── messaging/
│   ├── observability/
│   └── testing/
│
├── diagrams/
│   ├── architecture/
│   ├── sequences/
│   └── state-machines/
│
├── docker/
│   ├── compose.yaml
│   └── services/
│
├── scripts/
│   ├── setup/
│   ├── load-testing/
│   └── failure-simulation/
│
└── tests/
    ├── integration/
    ├── contract/
    └── end-to-end/
```

Cada diretório em `examples/` representa um grupo de problemas. As explicações principais permanecem neste README, enquanto o código demonstra as implementações.

---

# Mapa de conteúdos

## 1. Arquitetura e modelagem de domínio

### 01. Modelagem orientada ao domínio

Como transformar processos reais em entidades, serviços, eventos e regras de negócio. O exemplo pode utilizar pedidos, pagamentos ou negociações comerciais para mostrar por que modelos genéricos costumam espalhar regras pela aplicação.

### 02. Bounded Contexts

Uma mesma palavra pode ter significados diferentes em cada parte da empresa. “Cliente”, por exemplo, pode representar um comprador no e-commerce, uma conta no financeiro e uma oportunidade no CRM. O tópico mostrará como separar esses contextos.

### 03. Aggregates e invariantes

Aggregates definem fronteiras dentro das quais determinadas regras precisam permanecer consistentes. Um pedido, por exemplo, não pode ser confirmado sem possuir itens e um valor total válido.

### 04. Máquinas de estado

Operações como pedidos, pagamentos e contratos normalmente possuem transições permitidas e proibidas. Uma máquina de estado ajuda a impedir mudanças inválidas, como transformar diretamente um pagamento cancelado em aprovado.

### 05. Monólito modular

Como dividir uma aplicação em módulos bem definidos sem adotar microsserviços prematuramente. O exemplo demonstrará comunicação entre módulos, encapsulamento e prevenção de dependências circulares.

### 06. Arquitetura hexagonal

Separação entre regras de negócio e detalhes externos, como banco de dados, gateways de pagamento e sistemas de mensageria. O objetivo é tornar o domínio testável e reduzir o acoplamento com tecnologias específicas.

### 07. CQRS

Separação entre modelos de escrita e leitura quando as necessidades de consulta são muito diferentes das regras de alteração. Um dashboard comercial, por exemplo, pode utilizar uma projeção otimizada sem comprometer o modelo transacional.

### 08. Event Sourcing

Persistência das mudanças como uma sequência de eventos, em vez de armazenar apenas o estado atual. O exemplo financeiro mostrará como reconstruir uma transação, auditar alterações e produzir novas projeções.

---

## 2. Consistência e confiabilidade

### 09. Chaves de idempotência

Como garantir que uma operação crítica seja executada uma única vez, mesmo quando o cliente repete a requisição. O exemplo pode simular a criação de pagamentos ou pedidos com uma chave enviada no cabeçalho.

### 10. Deduplicação de operações

Idempotência e deduplicação estão relacionadas, mas não são exatamente a mesma coisa. Este tópico mostrará como identificar comandos, eventos ou mensagens que já foram processados.

### 11. Concorrência otimista

Uso de versões, timestamps ou condições de atualização para detectar alterações concorrentes. Pode ser aplicado quando dois operadores tentam modificar simultaneamente uma oportunidade comercial ou um pedido.

### 12. Concorrência pessimista

Bloqueio explícito de registros quando o risco de conflito é alto. O exemplo discutirá reservas de estoque e mostrará por que manter locks por muito tempo pode reduzir drasticamente a capacidade do sistema.

### 13. Consistência forte e consistência eventual

Nem todos os dados precisam ser atualizados imediatamente em todos os lugares. O conteúdo comparará saldos financeiros, catálogos de produtos, dashboards e mecanismos de busca.

### 14. Saga Pattern

Coordenação de operações que atravessam diferentes serviços ou módulos. Um pedido pode precisar reservar estoque, processar pagamento, emitir nota e iniciar a entrega sem depender de uma única transação de banco.

### 15. Transações compensatórias

Em sistemas distribuídos, nem sempre existe um rollback técnico. O conteúdo mostrará como desfazer o efeito de uma operação por meio de novas ações, como estornar um pagamento ou liberar uma reserva.

### 16. Transactional Outbox

Como alterar o banco de dados e registrar um evento de forma atômica. O exemplo evitará o problema em que o pedido é salvo, mas o evento responsável por iniciar o pagamento nunca é publicado.

### 17. Inbox Pattern

Persistência das mensagens recebidas antes ou durante seu processamento. Essa abordagem ajuda consumidores a detectar duplicidades e processar eventos com maior segurança.

### 18. Retry, backoff e jitter

Repetir uma operação imediatamente pode aumentar uma indisponibilidade. O tópico mostrará políticas de repetição progressiva e variação aleatória para evitar que milhares de clientes tentem novamente ao mesmo tempo.

---

## 3. Sistemas distribuídos e mensageria

### 19. Timeout, deadline e cancelamento

Toda chamada externa precisa possuir um limite. O conteúdo explicará a diferença entre definir um timeout local, propagar um prazo máximo e cancelar operações que já não possuem utilidade.

### 20. Circuit Breaker

Como interromper temporariamente chamadas para um serviço que está falhando. O exemplo demonstrará os estados fechado, aberto e semiaberto em uma integração com um gateway externo.

### 21. Bulkhead Pattern

Isolamento de recursos para impedir que a falha de uma funcionalidade derrube toda a aplicação. Um processamento pesado de relatórios, por exemplo, não deveria consumir todas as conexões necessárias ao checkout.

### 22. Filas versus streams

Filas normalmente distribuem tarefas entre consumidores, enquanto streams preservam uma sequência reutilizável de eventos. O tópico comparará processamento de pedidos, notificações, analytics e integração entre sistemas.

### 23. Semânticas de entrega

Discussão sobre entrega no máximo uma vez, pelo menos uma vez e exatamente uma vez. O conteúdo mostrará por que “exactly once” geralmente depende de várias garantias combinadas.

### 24. Particionamento e ordenação

Como distribuir eventos entre partições sem perder a ordem necessária. Operações de uma mesma conta bancária podem precisar utilizar a mesma chave de particionamento.

### 25. Consumer Groups

Divisão do processamento entre múltiplas instâncias de consumidores. O exemplo mostrará escalabilidade horizontal, rebalanceamento e os efeitos de adicionar ou remover consumidores.

### 26. Dead Letter Queue

Mensagens que falham repetidamente não devem bloquear indefinidamente a fila principal. O conteúdo demonstrará encaminhamento para DLQ, diagnóstico, correção e reprocessamento controlado.

### 27. Backpressure

Como impedir que produtores gerem trabalho em uma velocidade muito superior à capacidade dos consumidores. Serão explorados limites, buffers, desaceleração, rejeição e escalabilidade.

### 28. Webhooks confiáveis

Webhooks podem ser duplicados, atrasados, entregues fora de ordem ou nunca confirmados. O exemplo utilizará assinatura, identificador do evento, retry, idempotência e registro das tentativas.

---

## 4. Sistemas financeiros e pagamentos

### 29. Ledger de partidas dobradas

Em vez de atualizar diretamente um saldo, cada movimentação é registrada como débito e crédito. O tópico demonstrará como essa estrutura melhora auditoria, rastreabilidade e reconciliação.

### 30. Precisão monetária

Valores financeiros não devem ser tratados com ponto flutuante de maneira ingênua. O conteúdo abordará unidades mínimas, tipos decimais, arredondamento e distribuição de centavos.

### 31. Máquina de estados de pagamentos

Um pagamento pode passar por estados como criado, pendente, autorizado, capturado, liquidado, recusado e estornado. O exemplo impedirá transições inválidas e registrará o motivo de cada mudança.

### 32. Autorização, captura, liquidação e estorno

Essas etapas representam momentos distintos do fluxo financeiro. O conteúdo mostrará por que uma autorização aprovada ainda não significa necessariamente que o dinheiro foi liquidado.

### 33. Reconciliação financeira

Comparação entre registros internos e informações recebidas de bancos, adquirentes ou gateways. O exemplo detectará pagamentos ausentes, valores divergentes e operações duplicadas.

### 34. Auditoria e registros imutáveis

Sistemas financeiros precisam explicar quem realizou uma operação, quando ela aconteceu e qual era o estado anterior. O tópico discutirá trilhas de auditoria e limites da simples gravação de logs.

### 35. Detecção de risco e fraude

Como utilizar regras, limites e sinais comportamentais antes de aprovar uma operação. O foco será a arquitetura do fluxo de decisão, e não a criação de um sistema definitivo de prevenção a fraudes.

### 36. Proteção de dados financeiros

Tokenização, mascaramento, segregação de acesso e redução da exposição de informações sensíveis. O conteúdo demonstrará como evitar que dados críticos circulem desnecessariamente pela aplicação.

---

## 5. E-commerce e logística

### 37. Reserva de estoque

Adicionar um item ao carrinho não significa necessariamente reservar o produto. O tópico comparará reserva no carrinho, no checkout e após a confirmação do pagamento.

### 38. Prevenção de overselling

Dois clientes podem tentar comprar a última unidade ao mesmo tempo. O exemplo comparará locks, atualizações condicionais, filas e reservas temporárias.

### 39. Orquestração do ciclo de pedidos

Um pedido envolve estoque, pagamento, emissão, separação, entrega e notificações. O conteúdo mostrará como representar o fluxo sem concentrar toda a lógica em um único serviço.

### 40. Expiração e recuperação de carrinhos

Carrinhos podem expirar, ser abandonados ou existir em vários dispositivos. O tópico discutirá persistência, identificação de usuário, merge e políticas de limpeza.

### 41. Motores de preço e promoção

Regras como cupons, desconto progressivo, frete grátis e preços por segmento podem se tornar difíceis de manter. O exemplo apresentará uma estratégia extensível para avaliação de promoções.

### 42. Fulfillment e acompanhamento de entregas

Separação entre pedido comercial e processo logístico. O conteúdo modelará expedição, pacotes, transportadoras, tentativas de entrega e atualizações assíncronas.

### 43. Devoluções e reembolsos

Uma devolução pode envolver aprovação, recebimento do produto, atualização de estoque e estorno financeiro. O tópico mostrará como coordenar essas etapas e manter o histórico.

### 44. Consistência entre catálogo, busca e cache

O produto pode ser atualizado no banco e continuar desatualizado no mecanismo de busca ou cache. O exemplo explorará invalidação, eventos de atualização e consistência eventual.

---

## 6. Sistemas comerciais e plataformas

### 45. Pipeline comercial e transições de estágio

Oportunidades comerciais normalmente percorrem etapas como novo lead, contato, proposta, negociação e fechamento. O conteúdo mostrará como validar transições e preservar o histórico.

### 46. Comissões e atribuição de vendas

Calcular comissões envolve regras por produto, vendedor, equipe, período e origem da oportunidade. O exemplo demonstrará como versionar regras para não alterar resultados históricos.

### 47. Autorização com RBAC e ABAC

Papéis nem sempre são suficientes para expressar permissões reais. O conteúdo comparará controle baseado em função com políticas baseadas em atributos, organização, propriedade e contexto.

### 48. Observabilidade com logs, métricas e traces

Logs explicam eventos individuais, métricas mostram tendências e traces acompanham uma operação entre serviços. O exemplo correlacionará uma requisição desde o checkout até o processamento assíncrono.

### 49. Testes de integração e contratos

Mocks não garantem que dois serviços realmente consigam se comunicar. O tópico mostrará testes com infraestrutura real, contratos de API e validação de eventos.

### 50. Operação segura em produção

Feature flags, rollout gradual, migrations compatíveis, monitoramento e planos de recuperação reduzem o risco de mudanças. O conteúdo demonstrará como lançar uma funcionalidade sem depender de uma ativação total e irreversível.

---

# Primeiros exemplos recomendados

Para iniciar o repositório, os primeiros conteúdos podem formar um fluxo integrado de e-commerce:

## Exemplo 1 — Checkout idempotente

```text
Cliente
   |
   | POST /orders
   | Idempotency-Key: abc-123
   v
API de pedidos
   |
   +--> Verifica chave
   +--> Cria pedido
   +--> Armazena resposta
   +--> Retorna sempre o mesmo resultado para repetições
```

Conceitos envolvidos:

* idempotência;
* concorrência;
* armazenamento de respostas;
* expiração de chaves;
* restrições únicas;
* testes simultâneos.

## Exemplo 2 — Pedido com Transactional Outbox

```text
┌───────────────────────┐
│ Transação no banco    │
│                       │
│ 1. Salva o pedido     │
│ 2. Salva o evento     │
└───────────┬───────────┘
            |
            v
┌───────────────────────┐
│ Publicador da Outbox  │
└───────────┬───────────┘
            |
            v
┌───────────────────────┐
│ Broker de mensagens   │
└───────────────────────┘
```

Conceitos envolvidos:

* atomicidade;
* publicação assíncrona;
* reprocessamento;
* deduplicação;
* monitoramento do backlog.

## Exemplo 3 — Reserva concorrente de estoque

```text
Estoque disponível: 1 unidade

Cliente A ----\
               +--> tenta reservar --> apenas um vence
Cliente B ----/
```

Conceitos envolvidos

* concorrência otimista;
* locks;
* atualizações condicionais;
* expiração de reservas;
* prevenção de overselling.

## Exemplo 4 — Pagamentos orientados a eventos

```text
PaymentCreated
      |
      v
PaymentAuthorized
      |
      v
PaymentCaptured
      |
      v
PaymentSettled
```

Fluxos alternativos:

```text
PaymentAuthorizationFailed
PaymentCaptureFailed
PaymentCancelled
PaymentRefundRequested
PaymentRefunded
```

Conceitos envolvidos:

* Event Sourcing;
* máquina de estados;
* projeções;
* auditoria;
* idempotência;
* reconciliação.

## Exemplo 5 — Consumidor com retry e DLQ

```text
Mensagem recebida
       |
       v
Processamento
   |       |
 sucesso   falha
   |       |
   v       v
 ACK     Retry
           |
           v
     Limite excedido
           |
           v
          DLQ
```

Conceitos envolvidos:

* mensagens problemáticas;
* backoff;
* jitter;
* poison messages;
* reprocessamento;
* alertas.

---

# Tecnologias possíveis

Os exemplos não precisam ficar presos a uma única stack, mas podem compartilhar uma base comum.

## Aplicações

* TypeScript;
* Node.js;
* NestJS;
* Python;
* FastAPI.

## Bancos de dados

* PostgreSQL;
* Redis;
* SQLite para exemplos menores.

## Mensageria (MQ)

* RabbitMQ;
* Apache Kafka;
* Redis Streams;
* filas baseadas em banco para exemplos introdutórios.

## Infraestrutura local

* Docker;
* Docker Compose;
* scripts de setup;
* dados iniciais reproduzíveis.

## Observabilidade

* OpenTelemetry;
* Prometheus;
* Grafana;
* logs estruturados.

## Testes

* testes unitários;
* testes de integração;
* testes de contrato;
* testes end-to-end;
* testes de concorrência;
* testes de carga;
* simulação de falhas.

---

# Padrão mínimo de qualidade

Cada implementação deverá buscar:

* código fortemente tipado;
* ausência de regras de negócio nos controllers;
* validação explícita de entrada;
* tratamento de erros conhecido;
* logs estruturados;
* correlação entre requisições;
* migrations reproduzíveis;
* testes de integração;
* documentação das decisões;
* exemplos de falha;
* instruções de execução local.

---

# O que este repositório não pretende ser

Este projeto não pretende:

* apresentar padrões como soluções universais;
* utilizar microsserviços em todos os exemplos;
* esconder complexidade atrás de abstrações;
* criar benchmarks sem contexto;
* substituir documentação oficial;
* sugerir arquiteturas sem discutir trade-offs;
* tratar sistemas distribuídos como se fossem transações locais.

---

# Roadmap

## Fase 1 — Fundamentos críticos

* [ ] Checkout idempotente
* [ ] Deduplicação de eventos
* [ ] Concorrência otimista
* [ ] Reserva de estoque
* [ ] Transactional Outbox
* [ ] Consumidor com retry e DLQ

## Fase 2 — Domínio financeiro

* [ ] Ledger de partidas dobradas
* [ ] Máquina de estados de pagamentos
* [ ] Event Sourcing financeiro
* [ ] Reconciliação
* [ ] Estorno e compensação
* [ ] Auditoria de operações

## Fase 3 — Sistemas distribuídos

* [ ] Saga orquestrada
* [ ] Saga coreografada
* [ ] Circuit Breaker
* [ ] Backpressure
* [ ] Particionamento de eventos
* [ ] Webhooks confiáveis

## Fase 4 — Domínios comerciais

* [ ] Pipeline de vendas
* [ ] Histórico de oportunidades
* [ ] Motor de comissões
* [ ] Controle de acesso por organização
* [ ] Projeções para dashboards
* [ ] Importação idempotente de dados

## Fase 5 — Operação

* [ ] OpenTelemetry
* [ ] Métricas de filas
* [ ] Testes de contrato
* [ ] Testes de carga
* [ ] Simulação de falhas
* [ ] Feature flags e rollout gradual

---

# Como contribuir

Contribuições podem incluir:

* novos cenários de negócio;
* implementações alternativas;
* correções;
* testes;
* diagramas;
* documentação;
* análise de trade-offs;
* exemplos de falhas reais devidamente anonimizados.

Antes de propor uma implementação, procure responder:

1. Qual problema real está sendo resolvido?
2. Por que uma solução mais simples não seria suficiente?
3. Quais falhas precisam ser consideradas?
4. Como a solução será observada em produção?
5. Quais custos ela adiciona?
6. Como demonstrar seu comportamento por meio de testes?

---

# Ideias de contribuição

Alguns desafios que podem se transformar em exemplos:

* impedir pagamentos duplicados;
* garantir uma única comissão por venda;
* processar pedidos em lote;
* reprocessar mensagens de uma DLQ;
* reconstruir uma projeção a partir de eventos;
* detectar divergências financeiras;
* impedir estoque negativo;
* processar webhooks fora de ordem;
* versionar regras comerciais;
* distribuir centavos em uma divisão;
* preservar o histórico de alterações;
* lançar uma funcionalidade gradualmente.

---

# Público-alvo

Este repositório é voltado para:

* desenvolvedores backend;
* engenheiros de software;
* arquitetos de software;
* desenvolvedores full stack;
* estudantes que desejam ir além de CRUDs;
* profissionais se preparando para system design;
* equipes construindo sistemas críticos.

---

# Filosofia final

> Engenharia de software não é apenas fazer o caminho feliz funcionar.
>
> É garantir que o sistema continue compreensível quando requisições forem repetidas, mensagens chegarem fora de ordem, serviços ficarem indisponíveis e regras de negócio se tornarem mais complexas.

A qualidade de uma arquitetura não é medida apenas pelo diagrama.

Ela também é medida por sua capacidade de:

* preservar dados;
* limitar falhas;
* explicar decisões;
* permitir evolução;
* fornecer evidências;
* recuperar-se de situações inesperadas.

Este repositório existe para estudar exatamente esses problemas.
