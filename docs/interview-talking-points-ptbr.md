# ReleaseHub — pontos de conversa para entrevista

Roteiro de entrevista baseado nas decisões documentadas neste showcase público e sanitizado.

## Resumo em 30 segundos

> O ReleaseHub governa releases em ambientes privados ou híbridos, conectando pacote, aprovação, agendamento, execução, validação e auditoria. Sou responsável pelo desenho, pela arquitetura e pela construção do produto. Um case técnico é o rollback: como a operação envolve o ambiente remoto e o catálogo local, o sistema precisa manter os dois estados coerentes mesmo quando há falha no meio do fluxo.

## Case: rollback como saga recuperável

### Cenário e papel

- O ReleaseHub organiza execução e auditoria de releases self-hosted.
- Sou responsável pelo desenho, pela arquitetura e pela construção do produto.

### Desafio e decisão

- **Desafio:** o rollback remoto pode concluir antes de o catálogo local registrar a versão restaurada. Uma falha nessa janela deixa o resultado distribuído entre dois sistemas.
- **Decisão:** tratar o rollback como uma saga com estado intermediário explícito, operação idempotente e reconciliação.

### Funcionamento

1. Reservar a release por transição condicional de `Sucesso` para `Revertendo`.
2. Executar o rollback remoto com a chave idempotente da operação.
3. Atualizar o catálogo local para a versão anterior.
4. Marcar `Revertida` somente depois da confirmação remota e da atualização do catálogo.
5. Manter falhas recuperáveis em `Revertendo` para reconciliação; falhas permanentes permanecem explícitas e têm caminho de recuperação controlado.

### Consequência técnica e trade-off

O estado intermediário evita declarar o rollback concluído antes de confirmar os passos remotos e locais. A recuperação passa a exigir estado persistido, idempotência e reconciliação, o que adiciona complexidade ao fluxo; em troca, a divergência não fica escondida como sucesso.

### Resposta curta sugerida

> No rollback, há um efeito remoto e uma atualização do catálogo local; não existe uma única transação que cubra os dois lados. Modelei o fluxo como uma saga: primeiro reservo a operação em `Revertendo`, depois executo o rollback remoto e atualizo o catálogo, e só então concluo como `Revertida`. Se houver falha recuperável, o estado intermediário permite reconciliação. A decisão torna o estado real mais explícito e recuperável, com o custo de manter essa máquina de estados e o processo de recuperação.

## Perguntas para aprofundar

1. Por que a transição para `Revertendo` ocorre antes do efeito remoto?
2. O que impede uma repetição de aplicar o rollback duas vezes?
3. Em que condição o catálogo pode marcar a release como `Revertida`?
4. Como o sistema diferencia falha recuperável de falha permanente?

## Limites do material público

O showcase não publica código-fonte completo, credenciais, clientes, endpoints operacionais ou métricas de negócio. Não afirmar redução de falhas, tempo de release ou disponibilidade sem fonte autorizada.
