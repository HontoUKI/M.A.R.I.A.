# M.A.R.I.A.-Voice Status

`M.A.R.I.A.-Voice` is now an official external ecosystem module.

Responsibility:

- STT provider adapters;
- TTS provider adapters;
- VAD / turn segmentation;
- playback lifecycle;
- local voice diagnostics;
- narrow gateway calls to Core public API.

Boundary:

- Voice does not own Maria's personality, memory, feelings, or final reply
  generation;
- Voice talks to Core through public HTTP/API contracts;
- Voice does not import Core internals or read private Core runtime state.

Current local TTS baseline:

- provider: Silero TTS;
- speaker: `baya`;
- output: 48 kHz WAV;
- post-processing: subtle WAV polish;
- role: local fallback / current preferred offline voice baseline.

Edge TTS remains a comparison provider, but it is not the current preferred
Maria voice baseline because its prosody sounded too announcer-like in manual
listening.
