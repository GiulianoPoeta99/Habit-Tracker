# TODO: Backend Habit Tracker con Wisp (estructura por slices)

## 1. Infraestructura común

* [ ] Inicializar proyecto Gleam
* [ ] Configurar `rebar.config` con dependencias:

  * `wisp`
  * `gleam_sql` (o la librería DB que prefieras)
* [ ] Crear árbol de carpetas base:

  ```tree
  src/
  ├── user/
  ├── habit/
  └── tracker/
  tests/
  ```

## 2. Estructura de cada slice

Cada slice tendrá esta jerarquía:

```tree
<slice>/
├─ domain/
│  ├─ entities/
│  ├─ errors/
│  ├─ validators/
│  ├─ strategies/        # (o ports)
│  │   ├─ repositories/
│  │   ├─ services/
│  │   └─ factories/
│  └─ value_objects/
│      ├─ enums/
│      └─ types/
├─ application/
│  ├─ use_cases/
│  └─ dtos/
└─ infrastructure/
   └─ presentation/
      └─ http/
         └─ rest/
            ├─ controllers/
            ├─ routes/
            ├─ dtos/
            │  ├─ requests/
            │  ├─ responses/
            └─ middleware/     # p.ej. auth, logging, parsing JSON
```

### Estructura de tests

```tree
tests/
└── habits/
    ├── domain/
    │   ├── entities/
    │   │   └── habit.unit.py
    │   ├── errors/
    │   │   ├── habit_error.unit.py
    │   │   └── habit_repository_error.unit.py
    │   └── strategies/
    │       └── repositories/
    │           ├── habit_repository.unit.py
    │           └── habit_repository.integration.py
    │
    ├── application/
    │   ├── CreateHabit/
    │   │   ├── contract.unit.py
    │   │   └── create_habit.unit.py
    │   ├── GetHabit/
    │   │   ├── contract.unit.py
    │   │   └── get_habit.unit.py
    │   ├── ListHabits/
    │   │   ├── contract.unit.py
    │   │   └── list_habits.unit.py
    │   ├── UpdateHabit/
    │   │   ├── contract.unit.py
    │   │   └── update_habit.unit.py
    │   └── DeleteHabit/
    │       ├── contract.unit.py
    │       └── delete_habit.unit.py
    │
    ├── infrastructure/
    │   ├── persistence/
    │   │   ├── model/
    │   │   │   └── pg_habit_model.unit.py
    │   │   ├── mappers/
    │   │   │   └── pg_habit_mapper.unit.py
    │   │   └── strategies/
    │   │       ├── pg_habit_repository.unit.py
    │   │       └── pg_habit_repository.integration.py
    │   └── presentation/
    │       └── http/
    │           ├── contracts/
    │           │   ├── request/
    │           │   │   ├── create_habit.req.unit.py
    │           │   │   ├── get_habit.req.unit.py
    │           │   │   └── … (otros .req.unit.py)
    │           │   └── response/
    │           │       ├── habit.resp.unit.py
    │           │       └── … (otros .resp.unit.py)
    │           ├── handlers/
    │           │   ├── habit_handler.unit.py
    │           │   └── habit_handler.integration.py
    │           ├── mappers/
    │           │   ├── request_mapper.unit.py
    │           │   ├── response_mapper.unit.py
    │           │   └── handler_mapper.unit.py
    │           └── routes.unit.py
    │
    └── e2e/
        ├── habit_crud_flow.e2e.py
        ├── habit_list_filters.e2e.py
        └── habit_error_handling.e2e.py
```

## 3. TODO por slice

### 3.1 User

* [ ] **domain/entities**: definir `User(entity)`
* [ ] **domain/errors**: errores de validación (email inválido, password débil…)
* [ ] **domain/validators**: funciones de chequeo de email, contraseña
* [ ] **domain/strategies/repositories**: interfaz `UserRepo`
* [ ] **domain/strategies/services**: p.ej. `HashPasswordService`
* [ ] **domain/strategies/factories**: creación de instancias de repositorios/servicios
* [ ] **domain/value\_objects/enums**: roles de usuario (ADMIN, USER…)
* [ ] **domain/value\_objects/types**: `Email`, `PasswordHash`
* [ ] **application/use\_cases**:

  * `RegisterUser`
  * `LoginUser`
* [ ] **application/dtos**:

  * `RegisterUserRequest/Response`
  * `LoginUserRequest/Response`
* [ ] **infrastructure/presentation/http/rest/controllers**:

  * `UserController` con métodos `register`, `login`
* [ ] **infrastructure/presentation/http/rest/routes**:

  * `POST /users/register`
  * `POST /users/login`
* [ ] **infrastructure/presentation/http/rest/dtos/requests**:

  * esquemas JSON de entrada
* [ ] **infrastructure/presentation/http/rest/dtos/responses**:

  * esquemas JSON de salida
* [ ] Tests de integración y unitarios en `tests/user/`

### 3.2 Habit

* [ ] **domain/entities**: definir `Habit(entity)`
* [ ] **domain/errors**: errores (frecuencia inválida, título vacío)
* [ ] **domain/validators**: chequear frecuencia mínima, longitud de título
* [ ] **domain/strategies/repositories**: interfaz `HabitRepo`
* [ ] **domain/strategies/services**: lógicas auxiliares si hacen falta
* [ ] **domain/strategies/factories**
* [ ] **domain/value\_objects/enums**: tipos de frecuencia (DIARIO, SEMANAL, MENSUAL)
* [ ] **domain/value\_objects/types**: `Title`, `Frequency`
* [ ] **application/use\_cases**:

  * `CreateHabit`
  * `ListHabits`
* [ ] **application/dtos**:

  * `CreateHabitRequest/Response`
  * `ListHabitsResponse`
* [ ] **infrastructure/presentation/http/rest/controllers**:

  * `HabitController` con `create`, `list`
* [ ] **routes**:

  * `POST /habits`
  * `GET  /habits`
* [ ] **dtos/requests** y **dtos/responses** para JSON
* [ ] Tests en `tests/habit/`

### 3.3 Tracker

* [ ] **domain/entities**: definir `Entry(entity)`
* [ ] **domain/errors**: errores (marca fuera de frecuencia…)
* [ ] **domain/validators**: chequear intervalo mínimo entre ticks
* [ ] **domain/strategies/repositories**: interfaz `EntryRepo`
* [ ] **domain/strategies/services**: lógica de cálculo de rachas
* [ ] **domain/strategies/factories**
* [ ] **domain/value\_objects/types**: `Timestamp`
* [ ] **application/use\_cases**:

  * `TickHabit`
  * `GetHistory`
  * `CalculateStreaks`
* [ ] **application/dtos**:

  * `TickHabitRequest/Response`
  * `GetHistoryResponse`
* [ ] **infrastructure/presentation/http/rest/controllers**:

  * `TrackerController` con `tick`, `history`
* [ ] **routes**:

  * `POST /tracker/tick`
  * `GET  /tracker/history/:habit_id`
* [ ] JSON **dtos**
* [ ] Tests en `tests/tracker/`

## 4. Servidor Wisp global

* [ ] Crear `src/server.gleam`
* [ ] Inicializar `Router`, montar rutas de cada slice
* [ ] Agregar middleware global (JSON parser, logging, error handler)
* [ ] Configurar pool de DB (p. ej. `gleam_sql`) y pasarlo por contexto

## 5. Calidad y despliegue

* [ ] Makefile o scripts para compilar, testear y correr
* [ ] CI/CD (GitHub Actions)
* [ ] Dockerfile mínimo con puerto expuesto 8080
* [ ] Documentar endpoints en README u OpenAPI
