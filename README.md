# Pet360 API REST

Challenge FIAP 2026 - Java Advanced  
Empresa parceira: CLYVO VET  
Turma: 2TDSPW

Repositorio publico: https://github.com/joaolacerdaconsorte/java-challenge

## Equipe

| Nome | RM |
|---|---|
| João Vitor Lacerda | 565565 |
| Kauan Vieira de lima | 565403 |
| Murillo Fernandes Carapia | 564969 |

O projeto foi pensado para ajudar no acompanhamento continuo da saude dos pets. A API guarda dados de tutores, pets, clinicas, consultas, vacinas, medicamentos, agendamentos e alertas. A ideia principal é manter um historico organizado do pet, nao só fazer um CRUD simples.

## Tecnologias usadas

- Java 21
- Spring Boot 3.3.5
- Gradle com wrapper
- Spring Web
- Spring Data JPA
- H2 Database
- Bean Validation
- Spring Security com JWT
- Swagger/OpenAPI
- Cache simples do Spring
- HATEOAS

## Como rodar

Precisa ter Java 21 instalado. Nao precisa instalar Gradle, porque o projeto usa o wrapper.

No Windows:

```bash
gradlew.bat bootRun
```

No Linux/macOS:

```bash
./gradlew bootRun
```

A API sobe na porta 8080.

Links uteis:

- Swagger: http://localhost:8080/swagger-ui.html
- OpenAPI JSON: http://localhost:8080/v3/api-docs
- H2 Console: http://localhost:8080/h2-console
- JDBC do H2: `jdbc:h2:mem:pet360`
- usuario do H2: `sa`
- senha do H2: vazio

Quando a aplicação inicia ela cria as tabelas e carrega alguns dados para teste.

## Login

Existem dois usuarios ja carregados:

| Usuario | Senha | Perfil |
|---|---|---|
| admin | admin123 | ADMIN |
| user | user123 | USER |

Login:

```bash
curl -X POST http://localhost:8080/auth/login \
  -H "Content-Type: application/json" \
  -d "{\"username\":\"admin\",\"password\":\"admin123\"}"
```

As rotas de `/auth`, Swagger e H2 Console ficam liberadas. As rotas da API usam token Bearer.

Exemplo:

```bash
curl http://localhost:8080/api/pets \
  -H "Authorization: Bearer SEU_TOKEN"
```

## Principais rotas

Recursos com CRUD:

- `/api/tutores`
- `/api/clinicas`
- `/api/pets`
- `/api/consultas`
- `/api/vacinas`
- `/api/medicamentos`
- `/api/agendamentos`
- `/api/alertas`

Padrao usado nas entidades:

| Metodo | Exemplo | Retorno esperado |
|---|---|---|
| GET | `/api/pets` | lista paginada |
| GET | `/api/pets/{id}` | registro ou 404 |
| POST | `/api/pets` | 201 Created |
| PUT | `/api/pets/{id}` | registro atualizado |
| DELETE | `/api/pets/{id}` | 204 No Content |

Algumas rotas de consulta e regra de negocio:

| Rota | O que faz |
|---|---|
| `GET /api/pets/{id}/historico` | mostra historico do pet |
| `GET /api/pets/{id}/vacinas?vencidas=true&dias=30` | filtra vacinas vencidas ou proximas |
| `GET /api/pets/{id}/medicamentos?ativo=true` | lista medicamentos ativos |
| `GET /api/consultas?clinicaId=&petId=&status=&dataInicio=&dataFim=` | consulta com filtros |
| `GET /api/agendamentos/pendentes?clinicaId=` | lista agendamentos pendentes |
| `GET /api/alertas?tutorId=&lido=false` | lista alertas filtrados |
| `PATCH /api/alertas/{id}/marcar-lido` | marca alerta como lido |
| `PATCH /api/agendamentos/{id}/confirmar` | confirma agendamento |
| `PATCH /api/agendamentos/{id}/cancelar` | cancela agendamento |
| `PATCH /api/medicamentos/{id}/finalizar` | finaliza medicamento |

As listagens aceitam paginação, ordenação e filtros por parametro quando faz sentido.

Exemplo:

```text
GET /api/pets?page=0&size=10&sort=nome,asc
GET /api/pets?nome=rex&especie=CACHORRO
```

## O que foi usado para atender a entrega

- Entidades JPA relacionadas entre si
- Repositories com Spring Data JPA
- JPQL e query methods
- DTOs para entrada e saida
- Bean Validation nos requests
- Paginação e ordenação
- Busca por parametros
- Cache com `@Cacheable` e `@CacheEvict`
- Tratamento global de erros
- Swagger configurado
- Testes manuais exportados no Postman
- Documentos do DER, diagrama de classes, cronograma e arquitetura

## Documentos

A pasta `documentos` tem:

| Arquivo | Conteudo |
|---|---|
| `cronograma.md` | divisão das atividades da equipe |
| `DER.md` | diagrama entidade-relacionamento |
| `diagrama-classes.md` | diagrama das entidades Java |
| `arquitetura.md` | explicação simples das camadas da API |
| `pet360.postman_collection.json` | collection do Postman para testar a API |

## Estrutura

```text
java-challenge/
  build.gradle
  settings.gradle
  gradlew
  gradlew.bat
  README.md
  documentos/
  src/main/java/br/com/fiap/pet360/
    config/
    controller/
    dto/
    exception/
    model/
    repository/
    security/
    service/
  src/main/resources/application.yml
```

## Observação

O desenvolvimento começou em 30/04/2026. O repositório Git foi organizado e publicado em 24/05/2026 para a entrega de Java Advanced.
