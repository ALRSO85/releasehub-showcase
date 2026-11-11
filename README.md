# ReleaseHub — visão técnica

## Visão geral

ReleaseHub é uma plataforma self-hosted para governar e executar releases de software em ambientes privados ou híbridos. O fluxo conecta pacote, aprovação, agendamento, execução, validação e auditoria. Sou responsável pelo desenho, pela arquitetura e pela construção do produto.

[Acessar o ReleaseHub](https://releasehub.devforyou.com.br/) — o acesso às áreas operacionais requer uma conta autorizada.

## Arquitetura

O ReleaseHub é uma aplicação ASP.NET Core sobre .NET 10, com SQL Server. Roda em ambiente self-hosted no Ubuntu Server, em Docker, com Cloudflare Tunnel para acesso. O fluxo parte do catálogo e dos pacotes, passa por aprovação e agendamento e termina com execução, validação e auditoria.

```mermaid
flowchart LR
    Package[Pacote de software] --> Check[Verificação de integridade]
    Check --> Approval[Aprovação e agendamento]
    Approval --> Execution[Execução da release]
    Execution --> Validation[Validação]
    Validation --> Audit[Auditoria e evidências]
```

## Integridade da cadeia de artefatos

As verificações incluem SHA-256 e políticas configuráveis para exigir assinatura, proveniência e SBOM antes da implantação. A validação registra o resultado para apoiar rastreabilidade e decisões de release.

## Recuperação de rollback

O rollback é tratado como uma saga com estado intermediário. A release é reservada como `Revertendo` antes do efeito remoto; só passa a `Revertida` depois que a versão anterior é confirmada no catálogo local. Falhas permanecem explícitas e podem ser reconciliadas ou recuperadas com uma nova operação idempotente.

```mermaid
stateDiagram-v2
    [*] --> Sucesso
    Sucesso --> Revertendo: Reservar rollback de forma condicional
    Revertendo --> Revertida: Rollback remoto e versão anterior confirmados
    Revertendo --> Revertendo: Falha recuperável — reconciliar
    Revertendo --> FalhaRollback: Falha permanente ou dead-letter
    FalhaRollback --> Revertendo: Recuperação explícita com nova chave de operação
```

O catálogo local só confirma a versão anterior após a confirmação remota; um rollback falho ou incerto não se transforma silenciosamente em uma release bem-sucedida.

## Sobre este repositório

Este repositório apresenta decisões técnicas sem expor o código-fonte completo nem configurações operacionais privadas.
