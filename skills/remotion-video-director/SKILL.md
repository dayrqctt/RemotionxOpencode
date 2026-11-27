---
name: remotion-video-director
description: Master workflow produksi video Remotion (4 jalur input, Brief, Riset & Fact-Check, 99 Style Opuscar, TTS Emosional ElevenLabs/VoiceStudio, Preview Gate, Checklist, Export MP4 ke folder output portabel, Cleanup).
---

# Remotion Video Director

Master workflow produksi video terstruktur dengan Remotion (default 9:16 `1080x1920`, 30/60 fps, atau sesuai brief). Menggabungkan pipeline `remotion-workflow` (Fase 0–7, 99 style `lemo-opuscar`, TTS emosional) dengan disiplin produksi `3d-engineering-video` (4 jalur input, teardown referensi, budget suku kata/kata, tabel fact-check, clean master, catatan QA).

Ikuti seluruh fase di bawah ini secara berurutan. Jangan melompati fase gate (konfirmasi/audit) sebelum syaratnya terpenuhi.

---

## FASE 0: ORGANISASI PROYEK & WORKSPACE SEMENTARA

1. Pisahkan area kerja dari folder hasil agar folder output tetap bersih. **Jangan hardcode path mesin pribadi:** resolve path dari environment OS, lalu beri tahu user path aktualnya.
   - **Workspace sementara:** subfolder `remotion-<slug>` di direktori temp sistem — `${TMPDIR:-/tmp}/remotion-<slug>` (macOS/Linux) atau `%TEMP%\remotion-<slug>` (Windows). Override dengan `$REMOTION_WORKSPACE` bila user menyebut folder lain.
   - **Folder output final (eksklusif `.mp4`):** `$REMOTION_OUT`. Default: folder Documents milik user + `Remotion`. Resolve Documents lewat OS: PowerShell `[Environment]::GetFolderPath('MyDocuments')`, macOS/Linux `$HOME/Documents`. Buat folder bila belum ada.
   - **Template referensi (opsional):** cari `remotion-elevenlabs` atau `remotion-tips-9x16` di `$REMOTION_TEMPLATES_DIR` (default: folder Documents user). Jika tidak ada, scaffold proyek Remotion dari nol (`npx create-video@latest`, atau struktur manual sesuai Langkah 2). Ketidakhadiran template bukan alasan berhenti — lanjutkan dengan struktur standar.
2. Struktur rapi di dalam workspace sementara:
   - `docs/01-brief.md` — tema, ukuran, FPS, durasi, bahasa, target platform, style pilihan
   - `docs/02-research.md` — fakta terverifikasi + tabel fact-check + sumber primer (output skill `research`)
   - `docs/03-script.md` & `src/script.json` — naskah VO, `onScreenText`, `emotion`, dan `voiceSettings`
   - `docs/04-treatment.md` — storyline, POV, design system, shot list, camera grammar, sound plan
   - `public/audio/` — file TTS per scene
   - `public/bgm/` & `public/sfx/` — musik latar & efek suara
   - `public/assets/` — gambar, font, logo, data
   - `src/generated-manifest.json` — manifest audio + word-level timestamps
   - `out/stills/` — frame preview keyframe

---

## FASE 1: BRIEF, JALUR MASUK & SPESIFIKASI TEKNIS

### A. Empat Jalur Masuk

| Input dari User | Bahasa Video | Alur |
|---|---|---|
| Naskah siap pakai (EN) | English | Pakai naskah apa adanya (rapikan ejaan angka/singkatan), langsung fact-check + tulis visual plan |
| Naskah siap pakai (ID) | Indonesia | Pakai naskah apa adanya (rapikan ejaan angka/singkatan), langsung fact-check + tulis visual plan |
| Video referensi (EN/ID) | Bahasa lain (kecuali diminta sama) | Jalankan **Reference Teardown** dulu → riset fakta independen → tulis ulang naskah baru |
| Hanya topik | Konfirmasi bahasa | Riset fakta dulu → tulis naskah dari nol |

### B. Spesifikasi Teknis (`docs/01-brief.md`)

Tetapkan dan konfirmasi poin berikut sebelum riset atau menulis kode (isi default masuk akal bila tidak disebutkan user):
1. **Tema & Tujuan Video:** topik utama, pesan inti, dan CTA/penutup.
2. **Ukuran & Rasio Video:**
   - `1080x1920` (9:16 — Reels / Shorts / TikTok; wajib patuhi safe-zone UI bawah & kanan)
   - `1920x1080` (16:9 — YouTube / Presentasi)
   - `1080x1080` (1:1 — Feed)
3. **FPS & Durasi Target:** default `30` atau `60` fps, durasi target `30–75` detik (format explainer ideal `60–75` detik kecuali diminta lain).
4. **Budget Kata & Suku Kata (Akurat untuk Sync):**
   - **Bahasa Indonesia:** `~6.3 suku kata/detik` (atau `~2.2–2.5 kata/detik` natural) + ~2 detik jeda transisi.
   - **Bahasa Inggris:** `~2.7 kata/detik` + ~2 detik jeda transisi.
   - Jika naskah user jauh melebihi target, laporkan estimasi durasi dan sarankan baris yang bisa dipangkas (jangan memotong tanpa izin).
5. **Bahasa & Tone:** bahasa narasi, gaya penyampaian, serta apakah user sudah memilih style desain dari daftar 99 opsi `lemo-opuscar` (jika belum, rekomendasikan 2–3 style terbaik untuk topiknya).

---

## FASE 2: RISET, TEARDOWN REFERENSI & PENULISAN NASKAH

Gunakan skill terinstall: `research`, `antislop-copywriting`, `writing-beats`, `writing-shape`.

### A. Reference Teardown (Jika Ada Video Referensi)
- Ekstrak **grammar format saja**: durasi, tempo/pace, gaya caption, pola kamera (hard cuts vs motion), dan fungsi tiap beat (hook → rantai masalah-solusi → fakta kejutan → penutup balik ke hook).
- **Dilarang copy/dub mentah** kalimat, audio, atau karakter video referensi (kecuali user memberi video miliknya sendiri dan meminta terjemahan setia).
- Angka/klaim di video referensi hanya **petunjuk (leads)**, bukan sumber. Wajib verifikasi ulang di sumber primer.

### B. Riset & Tabel Fact-Check (`docs/02-research.md`)
1. Jalankan riset berbasis sumber primer/institusional (atau media teknis terpercaya yang dikonfirmasi lintas sumber) dengan skill `research`.
2. Simpan **tabel fact-check**: `Klaim | Sumber Primer | Penyederhanaan yang Dipilih`.
3. Jika ada klaim meragukan pada naskah user, laporkan beserta sumber dan usulkan kalimat pengganti (jangan ubah naskah secara diam-diam).
4. **Aturan Teks Layar:** Angka hanya boleh muncul di layar (`onScreenText`) jika sudah lolos tabel fact-check. Jika belum terverifikasi, gunakan label kualitatif tanpa angka.

### C. Penulisan Naskah (`docs/03-script.md` & `src/script.json`)
1. **Hook wajib di 3 detik pertama** (pertanyaan tajam, fakta mengejutkan, atau kontras visual).
2. Kalimat pendek, aktif, mudah diucapkan (speakable); hindari bahasa artikel atau klise AI (`antislop-copywriting`).
3. **Normalisasi TTS:** Tulis angka dalam bentuk kata ("lima puluh ton", "satu dari lima") dan eja singkatan sesuai pengucapan agar word-level timestamp & caption sinkron.
4. Pisahkan per scene (1 scene = 1 perubahan visual utama; pecah kalimat panjang pada koma):
   - `id` — identifier unik (`scene-1`, `scene-2`, ...)
   - `title` — judul/beat singkat
   - `text` — naskah VO yang dibacakan
   - `onScreenText` — keyword/highlight singkat di layar (jangan copy-paste seluruh VO ke layar)
   - `emotion` & catatan pacing — panduan untuk TTS Fase 4
5. Verifikasi total kata/suku kata sesuai budget durasi Fase 1.

---

## FASE 3: STORYLINE, POV, DESIGN & PENGAMBILAN GAMBAR

Gunakan skill terinstall: `lemo-opuscar`, `design-taste-frontend`, `antislop-ui`, `antislop-human`, `antislop-layoutmobile`.

### A. Pilihan Design Video (`lemo-opuscar`)
Jika style belum dipilih, rekomendasikan 2–3 opsi terbaik atau biarkan user memilih dari 99 style berikut. Setelah dipilih, wajib baca `$LEMO_OPUSCAR_HOME/styles/<folder>/STYLE.md` untuk mengadopsi palet warna, tipografi, motion grammar, camera grammar, dan sound palette ke komponen Remotion. Default library: `~/lemo-opuscar` (override `$LEMO_OPUSCAR_HOME`). Jika folder tidak ada, jalankan setup skill `lemo-opuscar` dulu, atau minta user menunjukkan lokasi library.

Daftar 15 kategori style:

1. **Edit & Documentary (8):** `caption-news`, `newsreel`, `countdown`, `city-symphony`, `visual-investigation`, `kino-montage`, `telestrator`, `news-package`
2. **Painting & Illustration (5):** `crayon-book`, `watercolor`, `impasto`, `urban-sketch`, `colored-pencil`
3. **Sketch & Board (7):** `one-line`, `whiteboard`, `chalkboard`, `sketchnote`, `stick-explainer`, `ipad-sketch`, `marker-paper`
4. **Traditional Crafts (7):** `ink-wash`, `shadow-puppet`, `ukiyoe`, `papercut-red`, `paper-lantern`, `stained-glass`, `gilded-illumination`
5. **Print & Printmaking (8):** `risograph`, `halftone-dossier`, `woodcut`, `engraving`, `silkscreen-poster`, `printed-long-take`, `pop-art`, `vintage-collage`
6. **Graphic & Type (8):** `swiss-motion`, `spy-titles`, `art-deco`, `pictogram-motion`, `bauhaus-shapes`, `flat-space-explainer`, `text-flash-cuts` ✦, `silhouette-rhythm`
7. **Line Diagrams (8):** `blueprint`, `iso-infographic`, `teardown`, `hardcore-info`, `precise-mechanism`, `instruction-manual`, `pen-plotter`, `assembly-line-diagram`
8. **Cartoons (6):** `rubber-hose`, `cel-anime-80s`, `scifi-toon`, `midcentury-toon`, `comic-history` ✦, `manga-panels` ✦
9. **Games (4):** `pixel-rpg`, `hd-2d`, `microgame`, `game-show`
10. **Film & Tape (5):** `ascii-crt`, `silent-film`, `backrooms`, `scratch-film`, `double-exposure`
11. **Screens & Interfaces (6):** `dark-keynote`, `living-screencast`, `hologram-hud`, `desktop-flythrough`, `spreadsheet`, `one-bit-dither`
12. **Light & Lines (5):** `circuit-board`, `firefly-pointillism`, `flow-field`, `oscilloscope-math`, `neon-tubes`
13. **3D & Miniature (8):** `brick-toy`, `paper-popup`, `tilt-shift`, `lowpoly-island`, `glass-product`, `memphis-diorama`, `isometric-picture-book`, `crayon-world`
14. **Instruments & Evidence (8):** `satellite-briefing`, `briefing-board`, `lidar-survey`, `microscope`, `archive-lighttable`, `security-xray`, `dark-documentary`, `thermal-camera`
15. **Data & Maps (6):** `dataviz`, `white-model`, `data-physical`, `transit-map`, `map-explainer`, `pip-simulation`

*(Style bertanda `✦` membutuhkan generator gambar seperti `generate_image` sebelum animasi dibuat.)*

### B. Isi Wajib `docs/04-treatment.md`
1. **Storyline & Arc:** setup → eskalasi/turn → klimaks → ending (usahakan echo/bookend ke opening).
2. **POV (Point of View):** sudut pandang narasi (orang pertama, narator dokumenter, observer, atau dialog).
3. **Visual Design System:** palet warna (kontras memenuhi standar `antislop-human`), hierarki tipografi terbaca di resolusi target, dan layout/safe-zone.
4. **Shot List & Camera Grammar per Scene:** framing (`wide`, `medium`, `close-up`, `insert`), camera movement (`push-in`, `pull-out`, `pan`, `spring zoom`, `card flip`, `kinetic type`), dan variasi tempo (selingi cepat, lambat, dan jeda napas). Gunakan satu set/ruang konsisten bila memakai diagram/cutaway.
5. **Sound Design Plan:** ambience bed, foley/SFX di momen aksi/transisi, dan musik latar (BGM ducked di bawah VO).

---

## FASE 4: PRODUKSI TTS EMOSIONAL & AUDIO-FIRST SYNC

Pilih provider TTS sesuai kebutuhan di Brief (atau mode hybrid: **VoiceStudio lokal** untuk draft/iterasi gratis, **ElevenLabs** untuk final komersial/intonasi Indonesia):

### Opsi A: ElevenLabs CLI (`elevenlabs`) + `@remotion/captions` (Alternatif: `@elevenlabs/elevenlabs-js` / MCP `elevenlabs`)
1. **Pastikan Auth CLI Aktif:**
   - Cek status login dengan `elevenlabs auth status` (jika belum aktif, jalankan `elevenlabs auth login`).
   - Cari `voice_id` yang cocok dengan karakter, gender, dan bahasa naskah via `elevenlabs voices --format json` (atau `elevenlabs voices search` sesuai subcommand tersedia).
2. **Wajib Emosional (Anti-Flat, Anti-Robotik, Anti-Kaku):**
   - Jangan kirim teks polos tanpa konteks. Wajib sertakan flag `--voice-settings.*` per scene, serta `--previous-text` dan `--next-text` dari scene tetangga agar intonasi antar scene mengalir natural:
     - **Hook / Antusias / Punchy:** `--voice-settings.stability 0.35 --voice-settings.similarity-boost 0.8 --voice-settings.style 0.45 --voice-settings.use-speaker-boost true --voice-settings.speed 1.03`
     - **Penjelasan Jelas / Edukatif:** `--voice-settings.stability 0.55 --voice-settings.similarity-boost 0.8 --voice-settings.style 0.25 --voice-settings.use-speaker-boost true --voice-settings.speed 1.0`
     - **Klimaks / Serius / Dramatis:** `--voice-settings.stability 0.40 --voice-settings.similarity-boost 0.85 --voice-settings.style 0.40 --voice-settings.use-speaker-boost true --voice-settings.speed 0.96`
     - **Penutup / Hangat / CTA:** `--voice-settings.stability 0.50 --voice-settings.similarity-boost 0.8 --voice-settings.style 0.30 --voice-settings.use-speaker-boost true --voice-settings.speed 0.98`
   - Gunakan tanda baca alami (`...`, `—`, koma, tanda seru/tanya secara proporsional) untuk mengatur jeda napas dan penekanan kata.
   - Eja istilah teknis/singkatan asing secara fonetik bila rawan salah ucap.
3. **Generate Speech + Timestamps via CLI (`convert_with_timestamps`):**
   - Jalankan per scene (POSIX shell; PowerShell: ganti `\` dengan backtick `` ` `` sebagai line continuation):
     ```sh
     elevenlabs text-to-speech convert_with_timestamps \
       --voice-id "<voice_id>" \
       --model-id "eleven_multilingual_v2" \
       --text "<scene.text>" \
       --previous-text "<prev_scene.text>" \
       --next-text "<next_scene.text>" \
       --voice-settings.stability 0.55 \
       --voice-settings.similarity-boost 0.8 \
       --voice-settings.style 0.25 \
       --voice-settings.use-speaker-boost true \
       --voice-settings.speed 1.0 \
       --output-format mp3_44100_128 \
       --format json
     ```
   - Decode `audio_base64` dari output JSON ke `public/audio/<scene-id>.mp3`, lalu konversi `alignment` (character-level timestamps) menjadi word-level timestamps di `src/generated-manifest.json` (untuk `@remotion/captions`).
   - Untuk SFX & BGM tambahan (Fase 3/4), gunakan `elevenlabs text-to-sound-effects convert` ke `public/sfx/` dan `elevenlabs music` ke `public/bgm/` bila dibutuhkan.

### Opsi B: VoiceStudio Lokal (Skill `voicestudio`, `http://localhost:3900`)
1. Pastikan backend VoiceStudio aktif (`GET http://localhost:3900/health`) dan temukan voice profile terinstall (`GET /v1/audio/voices`).
2. **Kontrol Ekspresi:**
   - Gunakan marker `[pause 500ms]`, `[slow]…[/slow]`, `[fast]…[/fast]`, `[emphasis]…[/emphasis]`, dan `paragraph_gap_ms` (hindari tag ElevenLabs seperti `[excited]` karena terbaca literal).
   - Untuk emosi natural Bahasa Indonesia, utamakan **reference clip cloning ekspresif**, atau engine opt-in (**IndexTTS2**, **CosyVoice 3**, **VoxCPM2**).
   - Untuk komersial, pastikan model berlisensi komersial (model default OmniVoice berlisensi `CC-BY-NC`).
3. **Word Timestamps untuk Remotion:**
   - Setelah audio per scene tersimpan ke `public/audio/`, panggil `POST http://localhost:3900/v1/audio/transcriptions` (`response_format=verbose_json`) untuk menghasilkan word-level timestamps ke `src/generated-manifest.json`.

### Aturan Umum Fase 4 (Semua Provider)
- Kunci durasi tiap scene dari panjang audio aktual via `CalculateMetadataFunction` + `mediabunny` (beri padding frame cukup agar akhir kalimat tidak terpotong).
- **Audio Check Gate:** cek `words.json` / `generated-manifest.json` — pastikan jumlah kata cocok dengan naskah dan catat kata/singkatan yang rawan meleset timing-nya. Jangan ulangi transkripsi setelah animasi scene mulai dibuat.

---

## FASE 5: IMPLEMENTASI VISUAL & PREVIEW GATE (WAJIB KONFIRMASI)

Gunakan skill terinstall: `typescript-write`, `antislop`, `antislop-ui`, `agent-browser`.
1. Bangun komponen scene modular di `src/` yang animasi dan subtitlenya terkunci ke timestamp kata di `src/generated-manifest.json`.
2. **DILARANG LANGSUNG EXPORT MP4 FINAL.**
3. Lakukan render preview cepat:
   - Render keyframe penting (hook, transisi, part swap, klimaks, outro) dengan `npx remotion still <CompId> out/stills/frame-<n>.png --frame=<n>`.
   - Inspeksi visual still frame tersebut secara langsung (jangan hanya percaya log).
   - Jalankan `npm run dev` (`remotion studio`) untuk preview gerak dan sinkronisasi audio secara real-time.
   - Jika perlu inspeksi tampilan web/studio secara visual, gunakan `agent-browser`.
4. **STOP & KONFIRMASI USER:** tampilkan hasil preview (`out/stills/`), total durasi aktual, dan catatan per scene. Tunggu persetujuan atau daftar revisi dari user sebelum ke Fase 6 & 7.

---

## FASE 6: PRE-EXPORT FINAL CHECKLIST

Gunakan skill terinstall: `typescript-review`, `antislop-code`, `antislop-human`.
Verifikasi seluruh poin berikut sebelum render final:
- [ ] **Typecheck & Lint:** `tsc --noEmit` dan `eslint src` lulus tanpa error.
- [ ] **Durasi & Sync:** tidak ada audio VO terpotong di ujung scene; word captions sinkron dengan ucapan.
- [ ] **Audio Mix:** VO dominan dan jernih, BGM di-ducking, SFX tepat di aksi/transisi, bebas clipping.
- [ ] **Visual & Safe-Zone:** tidak ada teks terpotong/overflow, kontras teks jelas, font dan seluruh `staticFile` ter-load sempurna.
- [ ] **Spesifikasi Output:** resolusi, FPS, codec (`h264`), dan pixel format (`yuv420p`) sesuai `docs/01-brief.md`.

---

## FASE 7: EXPORT FINAL, QA REPORT & CLEANUP

1. Pastikan folder output (`$REMOTION_OUT`, default `<Documents user>/Remotion`) ada, lalu render video final langsung ke:
   `<REMOTION_OUT>/<nama-video>.mp4`
   *(Opsional jika diminta: render juga clean master tanpa caption & tanpa BGM/SFX untuk kebutuhan editing lanjutan.)*
2. Verifikasi file `.mp4` hasil export:
   - File ada dan ukuran > 0 bytes.
   - Durasi serta stream video/audio valid (cek dengan `ffprobe`).
3. **Laporan QA ke User:**
   - Laporkan file output, durasi aktual, voice yang dipakai, dan item yang sudah dicek otomatis (`tsc`, `eslint`, `ffprobe`, safe-zone).
   - Nyatakan keterbatasan pengecekan (audio & motion tetap perlu didengar/ditonton manusia) serta daftar kata/detik yang rawan salah ucap atau pergeseran sync agar user tahu titik mana yang perlu disimak.
4. Setelah hasil `.mp4` terverifikasi dan tidak ada revisi lagi dari user, hapus workspace sementara (`$REMOTION_WORKSPACE`, default `<temp sistem>/remotion-<slug>`) agar tidak menyisakan file sampah.
5. Cek isi `$REMOTION_OUT` untuk memastikan hanya file `.mp4` hasil akhir yang tersisa.
