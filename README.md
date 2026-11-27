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

Salin isi folder ke direktori konfigurasi OpenCode (macOS/Linux `~/.config/opencode/`, Windows `%USERPROFILE%\.config\opencode\`):

- `commands/remotion.md` → `~/.config/opencode/commands/remotion.md`
- `skills/remotion-video-director/` → `~/.config/opencode/skills/remotion-video-director/`

## Konfigurasi Path Portabel (Opsional)

Semua path di-resolve otomatis per OS tanpa hardcode mesin lokal, dan bisa di-override via environment variable:

| Variabel | Fungsi | Default |
|---|---|---|
| `REMOTION_WORKSPACE` | Workspace sementara proyek | `${TMPDIR:-/tmp}/remotion-<slug>` (macOS/Linux) atau `%TEMP%\remotion-<slug>` (Windows) |
| `REMOTION_OUT` | Folder output `.mp4` final | `<Documents user>/Remotion` |
| `REMOTION_TEMPLATES_DIR` | Lokasi template opsional (`remotion-elevenlabs`, `remotion-tips-9x16`) | `<Documents user>` (jika tidak ada, scaffold proyek baru) |
| `LEMO_OPUSCAR_HOME` | Lokasi library `lemo-opuscar` | `~/lemo-opuscar` |

## Dependensi Eksternal

Workflow ini memanggil skill & resource eksternal berikut (harus terpasang terpisah jika dipakai):

- **Riset & Naskah:** `research`, `antislop-copywriting`, `writing-beats`, `writing-shape`
- **Desain & UI:** `lemo-opuscar` (style library), `design-taste-frontend`, `antislop-ui`, `antislop-human`, `antislop-layoutmobile`
- **TTS, Kode & QA:** `elevenlabs` CLI atau `voicestudio`, `typescript-write`, `typescript-review`, `antislop`, `antislop-code`, `agent-browser`

## Lisensi & Copyright

- **Repository ini (`commands/remotion.md` & `skills/remotion-video-director/SKILL.md`):**
  Copyright (c) 2026 **dayrqctt** — dirilis di bawah lisensi [MIT](LICENSE).

### Atribusi & Copyright Pihak Ketiga yang Digunakan

- **Lemo-Opuscar** — Copyright (c) 2026 SecondFirst Pty Ltd, trading as LemoLab ([github.com/lemomo-ai/lemo-opuscar](https://github.com/lemomo-ai/lemo-opuscar))
  - Docs/Prompts: `CC BY-NC 4.0`
  - Demo Films & Stills: `CC BY 4.0`
  - Code (`core/`, `tools/`, `plugin/`): `MIT License`
- **Remotion** (`remotion`, `@remotion/captions`, `@remotion/cli`) — Copyright (c) Remotion AG (Remotion License).
- **ElevenLabs** (`elevenlabs` CLI / `@elevenlabs/elevenlabs-js`) — Copyright (c) ElevenLabs Inc.
- **VoiceStudio** — Local TTS & transcription service (model default OmniVoice berlisensi `CC-BY-NC`).
- **Skill Pendukung Lainnya** (`research`, `antislop*`, `writing-beats`, `writing-shape`, `design-taste-frontend`, `typescript-write`, `typescript-review`, `agent-browser`) — hak cipta dan lisensi mengikuti masing-masing penulis/repositori aslinya.

