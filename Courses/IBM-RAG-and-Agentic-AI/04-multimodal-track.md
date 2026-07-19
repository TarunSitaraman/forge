# Multimodal Track (Course 5)

*Unlike the RAG and Agentic AI tracks, no canonical `Technologies/Docs/`
file covers multimodal generative AI yet. Capture real content here. If
it turns out substantial and reusable beyond this course, promote it to
a new `Technologies/Docs/multimodal-ai.md` following the same structure
as the other Docs files (Overview → Mental Model → Architecture →
Common Workflows → Common Mistakes → Best Practices → Cheatsheet →
Interview Questions → Further Reading) — don't promote prematurely on
thin content.*

## Course 5: Build Multimodal Generative AI Applications (8 hrs)

**Covers:** Multimodal AI integration for text, speech, images, and
video, using models including IBM Granite (vision), Llama, Whisper
(speech-to-text), DALL·E (image generation), and Sora (video
generation).

**Status:** Not started

**Labs/Notebooks:**
- [ ] Lab 1:
- [ ] Lab 2:
- [ ] Lab 3:

## Concepts to Capture As You Go

### Mental model (draft — refine as you learn)
*(What's the one framing that makes multimodal systems predictable
instead of memorized case-by-case? For RAG it's "retrieval vs.
generation are independent failure modes." What's the equivalent
insight here? Likely candidate: "each modality has its own
encode → align-to-shared-space → decode pipeline, and failures are
either modality-specific (bad transcription, bad image caption) or
alignment-specific (right transcription, wrong fusion into the shared
context).")*

### Architecture notes
- Text-to-speech / speech-to-text (Whisper) pipeline:
- Text-to-image (DALL·E) pipeline:
- Text-to-video (Sora) pipeline:
- Vision-language (Granite vision) pipeline:
- How multiple modalities get combined into one prompt/context:

### Common workflows
```python
# Fill in with real code from labs as completed
```

### Common mistakes encountered

### Model comparison notes

| Model | Modality | Notes |
|---|---|---|
| Granite (vision) | Image → text | |
| Whisper | Speech → text | |
| DALL·E | Text → image | |
| Sora | Text → video | |

## Decision Point: Promote to Canonical Doc?

After completing this course, answer honestly:
- [ ] Is this content substantial enough to be its own reference
      (200+ lines, genuinely reusable across future projects)?
- [ ] Will you actually reference multimodal AI concepts again outside
      this specific course?

If both yes → create `Technologies/Docs/multimodal-ai.md`, follow the
exact structure of the existing Docs files, register it in
`Technologies/Docs/_index.md`, and reduce this file back to a thin
course-tracker pointing there (matching how the RAG and Agentic AI
tracks work).

If either no → leave this as the durable home; it's fine for a single
course's worth of content to live in `Courses/` if it doesn't clear the
bar for a standalone technology reference.

## Next

[Agentic AI Track](./05-agentic-ai-track.md) or back to
[RAG & Vector DB Track](./03-rag-and-vector-db-track.md) if not yet done.
