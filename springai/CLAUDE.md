# CLAUDE.md

## Build & Run

```bash
# Set your DeepSeek API key
export DEEPSEEK_API_KEY=sk-xxx

# Build
mvn clean compile

# Run
mvn spring-boot:run

# Test the chat endpoint
curl -X POST http://localhost:8080/chat \
  -H "Content-Type: application/json" \
  -d '{"message":"你好，请介绍一下你自己"}'
```

## Tech Stack

- Spring Boot 3.4.7, Java 17
- Spring AI 1.0.2 (GA, on Maven Central)
- DeepSeek chat model via `spring-ai-starter-model-deepseek`

## Architecture

- `HelloApplication` — Spring Boot entry point
- `ChatController` — REST controller, `POST /chat` with JSON body `{"message": "..."}`
- `AiConfig` — wires `ChatClient` with `MessageWindowChatMemory` (backed by `InMemoryChatMemoryRepository`) for multi-turn conversation; `MessageChatMemoryAdvisor` automatically appends chat history to each request
- `application.yml` — DeepSeek config, model defaults to `deepseek-chat`, reads API key from `DEEPSEEK_API_KEY` env var

## Key Decisions

- Uses `ChatClient` (fluent API) rather than the lower-level `DeepSeekChatModel` directly — gives advisor support (chat memory) out of the box.
- `MessageWindowChatMemory` + `InMemoryChatMemoryRepository` keeps conversation history in-process with a sliding window (max 20 messages).
- API key read from env var, never hardcoded in config.