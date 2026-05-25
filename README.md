# 🎤 Speech API — Reconhecimento de Fala com Spring Boot

> Projeto do Bootcamp Java: **API Inteligente com Reconhecimento de Fala e Spring Boot**

---

## 🗺️ Roadmap Implementado

| Módulo | Tecnologia | Status |
|---|---|---|
| Persistência e Integração Real | Spring Data JPA + MongoDB | ✅ |
| Segurança com Spring Security e JWT | Spring Security 6 + JJWT | ✅ |
| Conectividade Externa (Feign) | Spring Cloud OpenFeign | ✅ |
| Microserviços de Áudio Especializados | Feign → Python/Whisper + gTTS | ✅ |
| Desacoplamento com MCP Server | Model Context Protocol Client | ✅ |
| Domínio Puro e Independência | Clean Architecture (Use Cases) | ✅ |
| Arquitetura Distribuída | Docker Compose multi-serviço | ✅ |

---

## 🏗️ Arquitetura

```
┌─────────────────────────────────────────┐
│           Spring Boot API (8080)         │
│                                         │
│  AuthController  TranscriptionController │
│  TtsController                          │
│         ↓                               │
│  [Use Cases - Domínio Puro]             │
│  AuthUseCase  TranscriptionUseCase      │
│  TtsUseCase                             │
│         ↓                               │
│  [Infrastructure]                       │
│  JPA → PostgreSQL (Users)               │
│  MongoDB (Transcriptions)               │
│  Feign → Python Transcription (8001)   │
│  Feign → Python TTS (8002)             │
│  HTTP  → MCP Server (8003)             │
└─────────────────────────────────────────┘
```

---

## 🚀 Como Executar

### Pré-requisitos
- Java 21+
- Maven 3.9+
- Docker + Docker Compose

### Desenvolvimento (H2 in-memory)

```bash
# Clonar / entrar no projeto
cd speech-api

# Rodar com perfil dev (H2, sem Docker)
mvn spring-boot:run

# Acessar Swagger UI
open http://localhost:8080/swagger-ui.html

# Acessar H2 Console
open http://localhost:8080/h2-console
```

### Produção (Docker Compose)

```bash
# Subir todos os serviços
docker-compose up -d

# Logs da API
docker-compose logs -f speech-api

# Parar tudo
docker-compose down
```

---

## 🔐 Autenticação

### 1. Registrar usuário
```bash
curl -X POST http://localhost:8080/api/auth/register \
  -H "Content-Type: application/json" \
  -d '{"name":"João","email":"joao@email.com","password":"senha123"}'
```

### 2. Login
```bash
curl -X POST http://localhost:8080/api/auth/login \
  -H "Content-Type: application/json" \
  -d '{"email":"joao@email.com","password":"senha123"}'
```

### 3. Usar token
```bash
export TOKEN="eyJhbGc..."

curl http://localhost:8080/api/transcriptions \
  -H "Authorization: Bearer $TOKEN"
```

---

## 🎤 Transcrição de Áudio

```bash
# Upload de arquivo de áudio
curl -X POST http://localhost:8080/api/transcriptions \
  -H "Authorization: Bearer $TOKEN" \
  -F "audio=@meu_audio.mp3" \
  -F "language=pt"

# Listar transcrições
curl http://localhost:8080/api/transcriptions \
  -H "Authorization: Bearer $TOKEN"

# Buscar por texto
curl "http://localhost:8080/api/transcriptions/search?q=reunião" \
  -H "Authorization: Bearer $TOKEN"
```

---

## 🔊 Text-to-Speech

```bash
# Converter texto em áudio
curl -X POST "http://localhost:8080/api/tts/synthesize" \
  -H "Authorization: Bearer $TOKEN" \
  --data-urlencode "text=Olá, bem-vindo ao bootcamp de Java!" \
  -d "language=pt-BR&format=mp3" \
  --output saida.mp3

# Listar vozes disponíveis
curl http://localhost:8080/api/tts/voices \
  -H "Authorization: Bearer $TOKEN"
```

---

## 📁 Estrutura do Projeto

```
src/main/java/com/bootcamp/speechapi/
├── SpeechApiApplication.java          # Entry point
├── config/
│   ├── SecurityConfig.java            # Spring Security + JWT
│   ├── AppConfig.java                 # Beans + OpenAPI/Swagger
│   └── GlobalExceptionHandler.java    # Tratamento global de erros
├── controller/
│   ├── AuthController.java            # POST /api/auth/**
│   ├── TranscriptionController.java   # CRUD /api/transcriptions
│   └── TtsController.java             # POST /api/tts/synthesize
├── application/
│   ├── usecase/
│   │   ├── AuthUseCase.java           # Registro e login
│   │   ├── TranscriptionUseCase.java  # Orquestra transcrição
│   │   └── TtsUseCase.java            # Delegação ao TTS
│   └── dto/
│       ├── request/AuthDTOs.java
│       └── response/ResponseDTOs.java
├── domain/
│   ├── model/
│   │   ├── User.java                  # Entidade JPA
│   │   └── Transcription.java         # Documento MongoDB
│   └── repository/
│       ├── UserRepository.java        # Spring Data JPA
│       └── TranscriptionRepository.java # Spring Data MongoDB
└── infrastructure/
    ├── feign/
    │   ├── TranscriptionServiceClient.java # Feign → Python Whisper
    │   └── TtsServiceClient.java           # Feign → Python TTS
    ├── mcp/
    │   └── McpServerClient.java            # MCP Server client
    └── security/
        ├── jwt/JwtService.java             # Geração/validação JWT
        └── filter/JwtAuthenticationFilter.java # Filtro HTTP
```

---

## 🧪 Testes

```bash
# Executar testes unitários
mvn test

# Relatório de cobertura
mvn verify
```

---

## ⚙️ Variáveis de Ambiente

| Variável | Padrão | Descrição |
|---|---|---|
| `JWT_SECRET` | (valor dev) | Chave secreta JWT |
| `MONGO_URI` | `mongodb://localhost:27017/speechdb` | URI do MongoDB |
| `TRANSCRIPTION_SERVICE_URL` | `http://localhost:8001` | URL do serviço Python de transcrição |
| `TTS_SERVICE_URL` | `http://localhost:8002` | URL do serviço Python TTS |
| `MCP_SERVER_URL` | `http://localhost:8003` | URL do MCP Server |
| `MCP_ENABLED` | `false` | Habilitar integração MCP |

---

## 📚 Tecnologias

- **Java 21** + **Spring Boot 3.2**
- **Spring Security 6** + **JWT (JJWT 0.12)**
- **Spring Data JPA** (PostgreSQL/H2)
- **Spring Data MongoDB**
- **Spring Cloud OpenFeign**
- **SpringDoc OpenAPI** (Swagger UI)
- **Lombok** + **MapStruct**
- **Docker** + **Docker Compose**

---

*Bootcamp Java — DIO | API Inteligente com Reconhecimento de Fala*
