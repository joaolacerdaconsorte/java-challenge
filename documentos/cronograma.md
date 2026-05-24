# Cronograma — Pet360 / Sprint 1

> Challenge FIAP — Java Advanced 2026 — CLYVO VET / Pet360
> Vale **até 5 pontos** (gestão de prazos e divisão clara de tarefas).

## Equipe

| Membro | RM | Turma |
|---|---|---|
| João Vitor Lacerda | 565565 | 2TDSPW |
| Kauan Vieira de lima | 565403 | 2TDSPW |
| Murillo Fernandes Carapia | 564969 | 2TDSPW |

O desenvolvimento começou em 30/04/2026. O repositório Git foi organizado e publicado no GitHub em 24/05/2026, junto com os ajustes finais da entrega.

## Visão geral da Sprint 1

A Sprint 1 cobre a entrega da API REST base do Pet360 com todos os requisitos do PDF do professor Luiz Real: persistência relacional, RESTful, validação, paginação, ordenação, busca, cache, exceções, DTOs, Swagger, JWT, HATEOAS e exportação Postman.

## Tabela de atividades

| # | Atividade | Responsável | Início | Fim previsto | Status |
|---|---|---|---|---|---|
| 1 | Análise do briefing e do PDF de entrega | João Vitor | 2026-04-30 | 2026-05-02 | Concluído |
| 2 | Modelagem do domínio (DER + Diagrama de Classes) | João Vitor | 2026-05-03 | 2026-05-07 | Concluído |
| 3 | Setup Spring Boot 3.3.5 + Gradle + H2 + dependências | João Vitor | 2026-05-08 | 2026-05-10 | Concluído |
| 4 | Implementação das 9 entidades JPA + 6 enums | João Vitor | 2026-05-10 | 2026-05-15 | Concluído |
| 5 | Repositórios (Spring Data JPA + JPQL) | João Vitor | 2026-05-15 | 2026-05-17 | Concluído |
| 6 | DTOs records + Bean Validation | Kauan | 2026-05-17 | 2026-05-19 | Concluído |
| 7 | Services com regras de negócio + Cache | Kauan | 2026-05-19 | 2026-05-21 | Concluído |
| 8 | Controllers REST + Swagger + HATEOAS | Murillo | 2026-05-21 | 2026-05-22 | Concluído |
| 9 | Spring Security + JWT (login/register/filtro) | João Vitor | 2026-05-22 | 2026-05-23 | Concluído |
| 10 | Tratamento global de exceções | Murillo | 2026-05-23 | 2026-05-23 | Concluído |
| 11 | Seed inicial de dados (DataInitializer) | João Vitor | 2026-05-23 | 2026-05-24 | Concluído |
| 12 | Documentação (README + diagramas + arquitetura) | Equipe | 2026-05-24 | 2026-05-24 | Concluído |
| 13 | Coleção Postman exportada | Kauan | 2026-05-24 | 2026-05-24 | Concluído |
| 14 | Testes manuais ponta a ponta dos endpoints | Equipe | 2026-05-24 | 2026-05-24 | Concluído |
| 15 | Push para GitHub público | João Vitor | 2026-05-24 | 2026-05-24 | Concluído |
| 16 | Envio do link da entrega | Equipe | 2026-05-24 | 2026-05-24 | Concluído |

## Critérios de pronto

Uma atividade só é marcada como concluída quando:
1. Código compila (`./gradlew compileJava`) sem erros
2. A aplicação sobe (`./gradlew bootRun`) sem exceções no log
3. Os endpoints relacionados respondem corretamente no Swagger UI
4. Pelo menos um teste manual via Postman foi feito

## Riscos identificados

| Risco | Mitigação |
|---|---|
| Uso de Oracle no lugar do H2 | Schema já modelado com nomes simples e compatíveis |
| Token JWT expirar durante a apresentação | Expiração configurada em 24h |
| Dados perdidos no H2 in-memory | Seed automático via `DataInitializer` a cada start |
