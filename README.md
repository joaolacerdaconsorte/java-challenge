# Pet360 — API REST

> **Challenge FIAP — Java Advanced 2026 (1º Sprint)**
> Empresa parceira: **CLYVO VET** | Branding: **Pet360**
> Equipe: João Vitor Lacerda RM 565565, Kauan Vieira de lima RM 565403 e Murillo Fernandes Carapia RM 564969 — Turma 2TDSPW

API REST em **Spring Boot 3.3.5** e **Java 21** que resolve o problema da CLYVO VET: ser o "sistema operacional do relacionamento contínuo entre clínica, responsável e pet". Persiste histórico longitudinal estruturado de tutores, pets, consultas, vacinas, medicamentos, agendamentos e alertas — indo muito além de CRUD puro.

---

## Sumário

- [Stack](#stack)
- [Como rodar](#como-rodar)
- [Autenticação JWT](#autenticação-jwt)
- [Endpoints principais](#endpoints-principais)
- [Estrutura do projeto](#estrutura-do-projeto)
- [Requisitos atendidos](#requisitos-atendidos-mapeados-ao-pdf)
- [Artefatos de entrega](#artefatos-de-entrega)
- [Equipe](#equipe)

---

## Stack

| Camada | Tecnologia |
|---|---|
| Linguagem | Java 21 |
| Build | Gradle 8.10 (wrapper) |
| Framework | Spring Boot 3.3.5 |
| Persistência | Spring Data JPA + Hibernate |
| Banco de dados | H2 in-memory (perfil default) |
| Segurança | Spring Security + JWT (jjwt 0.12) |
| Documentação | springdoc-openapi 2.6 (Swagger UI) |
| Validação | Bean Validation + hibernate-validator (`@CPF`, `@CNPJ`) |
| Cache | Spring Cache (in-memory) |
| HATEOAS | spring-boot-starter-hateoas |

---

## Como rodar

### Pré-requisitos
- Java 21 instalado (`java -version`)
- Não precisa instalar Gradle — o wrapper baixa sozinho

### Comandos

```bash
# Windows (PowerShell ou CMD)
gradlew.bat bootRun

# Linux / macOS / Git Bash
./gradlew bootRun
```

Ao iniciar, a aplicação:
1. Sobe na porta **8080**
2. Cria todas as tabelas no H2 (`ddl-auto: create-drop`)
3. Carrega seed inicial (2 tutores, 2 clínicas, 3 pets, 2 consultas, 3 vacinas, 2 medicamentos, 2 agendamentos, 2 alertas, 2 usuários)

### URLs úteis

| Recurso | URL |
|---|---|
| Swagger UI | http://localhost:8080/swagger-ui.html |
| OpenAPI JSON | http://localhost:8080/v3/api-docs |
| H2 Console | http://localhost:8080/h2-console |
| H2 JDBC URL | `jdbc:h2:mem:pet360` (user `sa`, sem senha) |

---

## Autenticação JWT

Dois usuários pré-cadastrados:

| Username | Password | Role |
|---|---|---|
| `admin` | `admin123` | ADMIN |
| `user` | `user123` | USER |

### Fluxo

```bash
# 1. Login
curl -X POST http://localhost:8080/auth/login \
  -H "Content-Type: application/json" \
  -d '{"username":"admin","password":"admin123"}'

# Resposta:
# {
#   "token": "eyJhbGciOiJIUzUxMiJ9...",
#   "username": "admin",
#   "role": "ADMIN",
#   "expiresInMs": 86400000
# }

# 2. Use o token em todas as outras requisições
curl http://localhost:8080/api/pets \
  -H "Authorization: Bearer <TOKEN>"
```

Endpoints `/auth/**`, `/swagger-ui/**`, `/v3/api-docs/**` e `/h2-console/**` são públicos. Todo o resto exige `Authorization: Bearer <token>`.

---

## Endpoints principais

### CRUD por entidade

Todos seguem o padrão RESTful (Richardson Maturity Model nível 3 com HATEOAS):

| Método | Path | Status |
|---|---|---|
| GET | `/api/<recurso>?page=&size=&sort=&...` | 200 (paginado) |
| GET | `/api/<recurso>/{id}` | 200 / 404 |
| POST | `/api/<recurso>` | 201 + `Location` |
| PUT | `/api/<recurso>/{id}` | 200 / 404 |
| DELETE | `/api/<recurso>/{id}` | 204 / 404 |

Recursos disponíveis: `tutores`, `clinicas`, `pets`, `consultas`, `vacinas`, `medicamentos`, `agendamentos`, `alertas`.

### Endpoints de negócio (vão além de CRUD)

| Endpoint | Descrição |
|---|---|
| `GET /api/pets/{id}/historico` | Timeline agregada do pet (consultas + vacinas + medicamentos + agendamentos) ordenada por data |
| `GET /api/pets/{id}/vacinas?vencidas=true&dias=30` | Vacinas vencidas ou próximas do vencimento na janela informada |
| `GET /api/pets/{id}/medicamentos?ativo=true` | Medicamentos em uso (controle de adesão) |
| `GET /api/consultas?clinicaId=&petId=&status=&dataInicio=&dataFim=` | Consultas filtradas por múltiplos critérios via JPQL |
| `GET /api/agendamentos/pendentes?clinicaId=` | Agendamentos PENDENTE futuros |
| `GET /api/alertas?tutorId=&lido=false` | Alertas não lidos do tutor |
| `PATCH /api/alertas/{id}/marcar-lido` | Marca alerta como lido |
| `PATCH /api/agendamentos/{id}/confirmar` | Confirma agendamento pendente |
| `PATCH /api/agendamentos/{id}/cancelar` | Cancela agendamento |
| `PATCH /api/medicamentos/{id}/finalizar` | Finaliza tratamento (ativo=false) |

### Paginação e ordenação

Todos os GETs de lista aceitam:
- `page` (default 0)
- `size` (default 20)
- `sort` (ex.: `sort=nome,asc` ou `sort=dataHora,desc`)

Exemplo: `GET /api/pets?nome=rex&especie=CACHORRO&sort=nome,asc&page=0&size=10`

### Cache

GETs por ID e listagens usam `@Cacheable`. Mutações disparam `@CacheEvict`. Cache names: `pets`, `tutores`, `clinicas`, `consultas`, `vacinas`, `medicamentos`, `agendamentos`, `alertas`.

---

## Estrutura do projeto

```
java-challenge/
├── build.gradle                          # Gradle, Spring Boot 3.3.5, Java 21
├── settings.gradle
├── gradlew, gradlew.bat, gradle/         # Gradle wrapper (auto-bootstrap)
├── .gitignore
├── README.md
├── documentos/
│   ├── cronograma.md                     # Cronograma da Sprint (5 pts)
│   ├── DER.md                            # Diagrama de Entidade-Relacionamento (Mermaid)
│   ├── diagrama-classes.md               # Diagrama de Classes (Mermaid)
│   ├── arquitetura.md                    # Camadas e fluxo de request
│   └── pet360.postman_collection.json    # Coleção Postman completa (10 pts)
└── src/main/
    ├── java/br/com/fiap/pet360/
    │   ├── Pet360Application.java
    │   ├── config/   OpenApiConfig, SecurityConfig, DataInitializer
    │   ├── controller/  9 controllers (Auth + 8 entidades)
    │   ├── dto/   Records de Request/Response + HATEOAS
    │   ├── exception/  GlobalExceptionHandler, erros customizados
    │   ├── model/  9 entidades JPA + 6 enums
    │   ├── repository/  9 JpaRepository com JPQL
    │   ├── security/  JwtService, JwtAuthFilter, UserDetailsService
    │   └── service/  8 services + AuthService (com cache e regras de negócio)
    └── resources/
        └── application.yml
```

---

## Requisitos atendidos (mapeados ao PDF)

| Requisito do PDF | Onde está implementado |
|---|---|
| **Spring Boot + JPA** | `build.gradle`, `model/*` |
| **POO** (herança, encapsulamento, polimorfismo) | Entidades com getters/setters explícitos, herança de `RepresentationModel` nos Response DTOs |
| **Coesão e desacoplamento** | Camadas separadas controller/service/repository, sem dependências cruzadas |
| **Design Patterns (com prudência)** | DTO, Repository, Service Layer, Strategy implícito (HATEOAS), Mapper estático em records |
| **RESTful (modelo de maturidade)** | Recursos no plural, verbos HTTP corretos, status codes, HATEOAS nível 3 |
| **JPQL / Query Methods** | Query Methods em `*Repository`, `@Query` JPQL em `search()` de cada repo |
| **Bean Validation** | `@NotBlank`, `@Email`, `@CPF`, `@CNPJ`, `@DecimalMin`, `@PastOrPresent` nos Requests |
| **Paginação** | `Pageable` em todos os GETs de lista (default size=20) |
| **Ordenação** | `Sort` via `?sort=campo,asc` |
| **Busca com parâmetros** | `@RequestParam` em todos os controllers |
| **Cache** | `@EnableCaching` + `@Cacheable`/`@CacheEvict` nos services |
| **Tratamento de exceções** | `GlobalExceptionHandler` com `@RestControllerAdvice` |
| **DTOs** | `dto/*` records (Java 21) |
| **Swagger** | `OpenApiConfig` + `@Tag`/`@Operation`/`@ApiResponse` nos controllers |
| **GitHub público** | https://github.com/joaolacerdaconsorte/java-challenge |
| **Postman exportado** | `documentos/pet360.postman_collection.json` |

### Diferenciais implementados (vão além do PDF)

- **Spring Security + JWT** stateless com filtro customizado
- **HATEOAS nível 3** (Richardson) com links em cada DTO de resposta
- **9 entidades** com relacionamentos coerentes ao DDL Oracle do aluno
- **Endpoints de negócio** que computam timeline, vacinas vencidas, medicamentos ativos

---

## Artefatos de entrega

Tudo dentro de `documentos/`:

| Arquivo | Pontos PDF | Conteúdo |
|---|---|---|
| `cronograma.md` | 5 | Quem faz o quê e quando |
| `DER.md` | 10 | Diagrama ER em Mermaid |
| `diagrama-classes.md` | 10 | Diagrama de Classes em Mermaid |
| `arquitetura.md` | (extra) | Camadas e fluxo |
| `pet360.postman_collection.json` | 10 | Collection completa para testes |

---

## Equipe

| Membro | RM | Turma | Responsabilidades |
|---|---|---|---|
| João Vitor Lacerda | 565565 | 2TDSPW | Arquitetura, entidades, segurança, documentação |
| Kauan Vieira de lima | 565403 | 2TDSPW | DTOs, services e coleção Postman |
| Murillo Fernandes Carapia | 564969 | 2TDSPW | Controllers, tratamento de erros e testes manuais |

Projeto iniciado em 30/04/2026 e publicado no GitHub em 24/05/2026 para a entrega da Sprint 1.

---

## Próximos passos (Sprint 2+)

- Persistir em Oracle FIAP (perfil `oracle` no application.yml)
- Adicionar regra automática de geração de alertas a partir de datas (job @Scheduled)
- Personalização IA: classificar pets em perfis de risco
- Push notifications via canal (Email/SMS/WhatsApp)
