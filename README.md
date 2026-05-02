# Salad Transcription API (salad-transcription-api)
Salad Transcription API converts speech to text using Salad's distributed GPU cloud network. Supports 97 languages, speaker diarization, word-level timestamps, and SRT caption generation for audio and video files.

**URL:** [Visit APIs.json URL](https://raw.githubusercontent.com/api-evangelist/salad-transcription-api/refs/heads/main/apis.yml)

## Scope

- **Type:** Index
- **Position:** Consuming
- **Access:** 3rd-Party

## Tags

- Audio Transcription, Captions, Diarization, GPU, Speech Recognition, Transcription, Video Processing

## Timestamps

- **Created:** 2024-11-17
- **Modified:** 2026-05-02

## APIs

### Salad Transcription API
Converts audio and video files to text with 97-language support, speaker diarization, and SRT output.

**Human URL:** [https://salad.com/transcription](https://salad.com/transcription)

#### Tags
- Audio Files, Audio Transcription, Diarization, Jobs, Speech Recognition, Transcriptions

#### Properties
- [Documentation](https://salad.com/transcription)
- [OpenAPI](openapi/salad-transcription-api-openapi.yml)
- [JSON Schema](json-schema/salad-transcription-job-schema.json)
- [JSON-LD Context](json-ld/salad-transcription-api-context.jsonld)
- [Spectral Rules](rules/salad-transcription-api-rules.yml)
- [Capabilities](capabilities/speech-to-text.yaml)
- [Vocabulary](vocabulary/salad-transcription-api-vocabulary.yml)

---

## Capabilities

### Shared Definitions

| File | Description |
|------|-------------|
| [capabilities/shared/salad-transcription-api.yaml](capabilities/shared/salad-transcription-api.yaml) | Shared consumed definition for Salad Transcription API |

### Workflow Capabilities

| File | Description | APIs |
|------|-------------|------|
| [capabilities/speech-to-text.yaml](capabilities/speech-to-text.yaml) | Speech-to-text transcription workflow | Salad Transcription API |

## Artifacts

| Artifact | Path |
|----------|------|
| OpenAPI Spec | [openapi/salad-transcription-api-openapi.yml](openapi/salad-transcription-api-openapi.yml) |
| JSON Schema | [json-schema/salad-transcription-job-schema.json](json-schema/salad-transcription-job-schema.json) |
| JSON Structure | [json-structure/salad-transcription-api-structure.json](json-structure/salad-transcription-api-structure.json) |
| JSON-LD Context | [json-ld/salad-transcription-api-context.jsonld](json-ld/salad-transcription-api-context.jsonld) |
| Spectral Rules | [rules/salad-transcription-api-rules.yml](rules/salad-transcription-api-rules.yml) |
| Vocabulary | [vocabulary/salad-transcription-api-vocabulary.yml](vocabulary/salad-transcription-api-vocabulary.yml) |

## Examples

- [Transcribe Media](examples/salad-transcribe-example.json)
- [Get Transcript](examples/salad-get-transcript-example.json)

## Common Properties

- [Postman Workspace](https://www.postman.com/salad-apis/salad/overview)
- [Pricing](https://salad.com/pricing)
- [Blog](https://blog.salad.com/)
- [Security](https://salad.com/security)
- [Privacy Policy](https://salad.com/privacy)
- [Terms of Service](https://salad.com/terms)
- [Trust Center](https://trust.salad.com/)

## Maintainers

**FN:** Kin Lane
**Email:** kin@apievangelist.com
