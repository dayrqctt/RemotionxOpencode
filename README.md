# Remotion Video Director (`/remotion`)

Workflow produksi video terstruktur dengan Remotion (Fase 0–7): 4 jalur input, brief, riset & fact-check, 99 style `lemo-opuscar`, TTS emosional (ElevenLabs CLI / VoiceStudio), preview gate, checklist QA, dan export `.mp4`.

## Struktur Folder

```text
remotion-video-director/
├── commands/
│   └── remotion.md                      # Slash command entrypoint (/remotion)
├── skills/
│   └── remotion-video-director/
│       └── SKILL.md                     # Definisi workflow lengkap Fase 0–7
├── .gitignore
└── README.md
```

## Instalasi Lokal (OpenCode)

Salin isi folder ke direktori konfigurasi OpenCode:

- `commands/remotion.md` → `~/.config/opencode/commands/remotion.md`
- `skills/remotion-video-director/` → `~/.config/opencode/skills/remotion-video-director/`

## Dependensi Eksternal

Workflow ini memanggil skill & resource eksternal berikut (harus terpasang terpisah jika dipakai):

- **Riset & Naskah:** `research`, `antislop-copywriting`, `writing-beats`, `writing-shape`
- **Desain & UI:** `lemo-opuscar` (style library), `design-taste-frontend`, `antislop-ui`, `antislop-human`, `antislop-layoutmobile`
- **TTS, Kode & QA:** `elevenlabs` CLI atau `voicestudio`, `typescript-write`, `typescript-review`, `antislop`, `antislop-code`, `agent-browser`
