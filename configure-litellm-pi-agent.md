# Configurar LiteLLM Gateway en Pi Agent

## Requisitos

- Pi Agent instalado (`npm i -g @earendil-works/pi-coding-agent`)
- Acceso al gateway: `https://myapi.fliasand.com/v1`
- API key provista por separado

## 1. Verificar conectividad

```bash
curl -s https://myapi.fliasand.com/v1/models \
  -H "Authorization: Bearer <API_KEY>" \
  | python3 -m json.tool
```

Debe devolver: `orchestrator`, `deep`, `architect`, `scout`, `ultrawork`, `local-deep`.

## 2. Crear provider en `~/.pi/agent/models.json`

Agregar el objeto `litellm` dentro de `"providers"`:

```json
{
  "providers": {
    "litellm": {
      "api": "openai-completions",
      "apiKey": "<API_KEY>",
      "baseUrl": "https://myapi.fliasand.com/v1",
      "headers": {
        "User-Agent": "pi-coding-agent"
      },
      "models": [
        {
          "_launch": true,
          "contextWindow": 262144,
          "id": "orchestrator",
          "input": ["text", "image"],
          "reasoning": true,
          "maxOutput": 262144
        },
        {
          "_launch": true,
          "contextWindow": 131072,
          "id": "deep",
          "input": ["text"],
          "reasoning": true,
          "maxOutput": 8192
        },
        {
          "_launch": true,
          "contextWindow": 131072,
          "id": "architect",
          "input": ["text"],
          "reasoning": true,
          "maxOutput": 8192
        },
        {
          "_launch": true,
          "contextWindow": 131072,
          "id": "scout",
          "input": ["text"],
          "reasoning": true,
          "maxOutput": 8192
        },
        {
          "_launch": true,
          "contextWindow": 262144,
          "id": "ultrawork",
          "input": ["text", "image"],
          "reasoning": true,
          "maxOutput": 262144
        },
        {
          "_launch": true,
          "contextWindow": 131072,
          "id": "local-deep",
          "input": ["text"],
          "reasoning": true,
          "maxOutput": 8192
        }
      ]
    }
  }
}
```

> **⚠️ El campo `headers` es obligatorio.** Sin `"User-Agent": "pi-coding-agent"`, Cloudflare bloquea el `User-Agent: OpenAI/JS` que el SDK envía por defecto (error 403/1010).

## 3. Agregar credencial en `~/.pi/agent/auth.json`

```json
{
  "litellm": {
    "type": "api_key",
    "key": "<API_KEY>"
  }
}
```

Mantener las credenciales existentes, solo agregar `litellm`.

## 4. Setear defaults en `~/.pi/agent/settings.json`

```json
{
  "defaultProvider": "litellm",
  "defaultModel": "orchestrator"
}
```

## 5. Verificar

```bash
pi --list-models | grep litellm
```

Debe mostrar los 6 modelos. Test rápido:

```bash
pi --provider litellm --model scout -p "Say OK" --no-session
```

## Uso

```bash
# Modelo default (orchestrator)
pi "Refactoriza este archivo"

# Modelo específico
pi --provider litellm --model architect "Diseña la API"
pi --provider litellm --model deep "Implementa el feature X"
pi --provider litellm --model scout "Busca todos los usos de fetch()"

# Ciclar modelos con Ctrl+P
pi --models "litellm/*" "Explora el codebase"
```

## Modelos virtuales

| Modelo | Motor | Uso |
|---|---|---|
| `orchestrator` | Kimi K2.6 | Orquestador general, sigue prompts largos |
| `deep` | DeepSeek V4 Pro | Deep worker autónomo, investigación + implementación |
| `architect` | GLM-5.1 | Planning estratégico, arquitectura |
| `scout` | DeepSeek V4 Flash | Rápido, retrieval, alta frecuencia |
| `ultrawork` | Kimi K2.6 Max | Máxima capacidad sin restricción de costo |
| `local-deep` | DeepSeek V4 Flash (Ollama) | Privacidad — datos no salen a APIs externas |

## Notas

- Los fallbacks entre proveedores los maneja LiteLLM del lado del gateway. Pi no necesita configurarlos.
- `architect` tiene rate limit ajustado (880 req/5hr). Si se satura, LiteLLM hace fallback automático.
- `deep` (DeepSeek V4 Pro) tiene ~94% tasa de alucinación cuando no sabe la respuesta. Verificar dependencias nuevas que genere.