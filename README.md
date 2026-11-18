# ClipForge

Alternativa open-source y self-hosted a OpusClip: vídeo largo → clips verticales 9:16 con subtítulos
animados palabra por palabra y reencuadre automático al hablante. Licencia MIT.

```
ingest → transcribe → highlights (LLM) → reframe (speaker) → subtitles (ASS) → render (FFmpeg)
```

| Modo | Transcripción | LLM |
|---|---|---|
| Local | faster-whisper | Ollama |
| Cloud | AssemblyAI | Claude / GPT |

## Instalación
Requiere Python ≥ 3.10 y `ffmpeg` en el PATH (fuente recomendada: Montserrat ExtraBold).
```bash
pip install -e ".[cloud,web,mcp,dev]"
cp .env.example .env
ollama pull qwen2.5:14b-instruct       # solo modo local
```

## Uso
```bash
clipforge analyze podcast.mp4 -n 5                 # propone highlights (sin renderizar)
clipforge run podcast.mp4 -n 5 --min 25 --max 60   # pipeline completo
clipforge run "https://youtu.be/XXXX" --provider anthropic --model claude-sonnet-5-5
clipforge serve                                    # API + UI en http://127.0.0.1:8000
clipforge mcp                                      # servidor MCP (stdio)
```
Salida en `workspace/<job>/clips/`. `transcript.json` y `highlights.json` actúan de caché y se pueden editar.

### MCP (Claude Desktop / Claude Code)
```json
{"mcpServers": {"clipforge": {"command": "clipforge", "args": ["mcp"]}}}
```
Herramientas: `analyze_video`, `create_clips`.

### Docker
```bash
docker compose up -d ollama web
docker compose exec ollama ollama pull qwen2.5:14b-instruct
# UI: http://127.0.0.1:8000     CLI: docker compose run --rm clipforge run /input/podcast.mp4
```
La API **no tiene autenticación**: no la expongas sin un proxy con auth.

## Configuración
Variables `CLIPFORGE_*` (ver `.env.example`): `WHISPER_MODEL`, `LLM_PROVIDER`, `LLM_MODEL`,
`TRANSCRIBER`, `SPEAKER_TRACKING`, `CAMERA_SMOOTH_S`, etc.

## Estado y limitaciones
Proyecto en fase inicial: el código ha sido escrito y revisado pero necesita pruebas reales con vídeos.
- Hablante activo por movimiento de labios: funciona mejor con caras frontales y bien iluminadas.
- Sin layout dividido para dos hablantes, sin B-roll, sin cancelación de jobs en la API.
- Los prompts de highlights están en `highlights/prompts.py`; ajústalos para tu nicho.

## Roadmap
B-roll automático · layout split multi-speaker · editor de highlights en la UI · cola persistente (Redis) ·
presets de estilo de subtítulos · publicación directa a TikTok/YouTube.

## Contribuir
`pytest` antes de abrir un PR. Issues y PRs bienvenidos.
