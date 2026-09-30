# ReleaseHub — showcase técnico

> Visão pública e sanitizada do produto. Este material não contém código-fonte, credenciais, dados de clientes ou configuração de produção.

## Visão geral

ReleaseHub é uma plataforma self-hosted para governar e executar releases de software em ambientes privados ou híbridos. O fluxo conecta pacote, aprovação, agendamento, execução, validação e auditoria.

[Acessar o ReleaseHub](https://releasehub.devforyou.com.br/) — o acesso às áreas operacionais requer uma conta autorizada.

## Desafio de produto

Coordenar mudanças de software exige contexto para a decisão, evidências de execução e uma forma clara de validar ou reverter uma release. O ReleaseHub organiza essas etapas em um fluxo rastreável.

## Arquitetura em alto nível

- **Aplicação:** ASP.NET Core sobre .NET 10.
- **Execução:** self-hosted, com suporte a Docker.
- **Persistência:** SQL Server.
- **Fluxo:** catálogo e pacotes → aprovação e agendamento → execução → validação e auditoria.

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

## Escopo deste showcase

Este resumo apresenta capacidades em alto nível. Não publica código-fonte, detalhes de autenticação, endpoints operacionais, segredos, dados de clientes, topologia ou configuração de ambientes.