# Optional OpenRouter image route

Use only with user authorization for this provider. Keep credentials in the target process, never in prompts, command literals, plugin files or logs. Prefer an existing secret manager. With AI Know Me installed, discover `llm.openrouter` by name and inject it:

```sh
ai-know-me search openrouter --json
ai-know-me run --env OPENROUTER_API_KEY=llm.openrouter -- \
  node scripts/openrouter-image.mjs prompts/direction-a.txt output/direction-a.png low
```

Resolve the script relative to this skill directory. An already configured `OPENROUTER_API_KEY` environment also works; AI Know Me is optional, not a bundled dependency. Never ask users to paste their key into chat.

For an authorized local style reference, add `--reference reference.png` after the quality argument. The adapter accepts one local PNG or JPEG up to 10 MiB, checks its file signature, and sends it inline via `input_references`. It records the reference count and SHA-256, not its image bytes, in the receipt. The image itself is transmitted to OpenRouter: use an appropriate user-provided design reference and describe its role in the prompt; do not automatically attach unrelated local files. Keep exact reference provenance in the project exploration, outside the distributable plugin.

Example: `node scripts/openrouter-image.mjs prompt.txt candidate.png low --reference style-board.png`. In the 2026-09-10 MiniDock test, all four requests returned images and `reference_count: 1`; total provider-reported cost was $0.050294. Reference input adds cost; this is a measurement, not a fixed quote. [Provider reference-image documentation](https://openrouter.ai/docs/guides/overview/multimodal/image-generation#image-to-image-reference-images).

The adapter sends one request to `https://openrouter.ai/api/v1/images` using `openai/gpt-image-2`, `aspect_ratio: 1:1`, `quality: low|medium|high` and `n: 1`. It expects an inline PNG in `data[0].b64_json`, writes PNG plus a safe `.png.json` receipt, refuses to overwrite the PNG, and never logs a provider error body. No automatic retries or fallback models. Review `http_401`, `http_403`, `http_429` or `generation_failed` without assuming an expired key from a generic error.

Verify current availability using the provider's public image model catalog before changing the adapter. Do not pass undocumented size or transparency parameters. The tested route returns an opaque image; for a graphic on white, matte removal is an explicit approximate postprocessing step, not native alpha generation.

Local MiniDock experiment: three low-quality 1024 square requests cost $0.021845 total as reported by the provider. This is a historical measurement, not a guaranteed quote. `quality` affects generation; PNG/JPEG compression after generation affects file size only. Do not persist raw API responses containing temporary asset URLs or debug headers.
