# MCP Tools Reference -- @ycse/nanobanana-mcp

> Load this on-demand when you need to check tool parameters or response formats.
> Do NOT load at startup.

## Package

`@ycse/nanobanana-mcp` -- configured via `/banana setup` or `setup_mcp.py`

**Required env:** `GOOGLE_AI_API_KEY` (from https://aistudio.google.com/apikey)

**Output directory:** `~/Documents/nanobanana_generated/`

## Tools

### gemini_generate_image

Generate a new image from a text prompt.

**Parameters:**
| Parameter | Type | Required | Default | Notes |
|-----------|------|----------|---------|-------|
| `prompt` | string | Yes | -- | The crafted prompt (NOT raw user text) |
| `aspectRatio` | string | No | `"1:1"` | Set via `set_aspect_ratio` first |
| `imageSize` | string | No | `"1K"` | UPPERCASE: `"512"`, `"1K"`, `"2K"`, `"4K"` |
| `model` | string | No | package default | Set via `set_model` |

**NOT supported (silently ignored):**
- `numberOfImages` -- Gemini generates 1 image per call
- `negativePrompt` -- no negative prompt API parameter exists
- `seed` -- not supported by Gemini image models

**Response:** Image saved to `~/Documents/nanobanana_generated/`, returns file path.

**finishReason values:**
- `STOP` -- Success
- `IMAGE_SAFETY` -- Output blocked (rephrase prompt, retry with user approval)
- `PROHIBITED_CONTENT` -- Content policy violation (non-retryable)
- `SAFETY` -- General safety block (rephrase prompt)
- `RECITATION` -- Copyrighted content detected (rephrase)

---

### gemini_edit_image

Modify an existing image using text instructions.

**Parameters:**
| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `imagePath` | string | Yes | Absolute path to input image |
| `prompt` | string | Yes | Enhanced edit instruction (NOT raw user text) |
| `model` | string | No | Defaults to current model |

**Supported input formats:** PNG, JPEG, WebP, GIF

**Response:** Edited image saved to `~/Documents/nanobanana_generated/banana_edit_*.png`

---

### gemini_chat

Multi-turn visual conversation that maintains context across turns.

**Parameters:**
| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `prompt` | string | Yes | Turn instruction or refinement |
| `imagePath` | string | No | Reference image for this turn |

**Use for:**
- Character consistency across multiple generations
- Progressive refinement ("now make the lighting warmer")
- Sequential storytelling / design sheets

**Session behavior:** Maintains style, characters, and context within a conversation.
Use `clear_conversation` to reset.

---

### set_aspect_ratio

Configure the output aspect ratio for subsequent generations.

**Call BEFORE `gemini_generate_image` when ratio differs from default (1:1).**

**Parameters:**
| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `ratio` | string | Yes | One of the 14 valid ratios |

**Valid ratios:**
`1:1`, `16:9`, `9:16`, `4:3`, `3:4`, `2:3`, `3:2`, `4:5`, `5:4`, `21:9`

Nano Banana 2 (3.1 Flash) only: `1:4`, `4:1`, `1:8`, `8:1`

---

### set_model

Switch between Gemini image models.

**Parameters:**
| Parameter | Type | Required | Notes |
|-----------|------|----------|-------|
| `model` | string | Yes | Model ID string |

**Valid models:**
- `gemini-3.1-flash-image-preview` -- Nano Banana 2 (default, recommended)
- `gemini-2.5-flash-image` -- Nano Banana original (budget, 1K max)

**DO NOT USE:** `gemini-3-pro-image-preview` -- shut down March 9, 2026

---

### get_image_history

Review images generated in the current session.

**Parameters:** None

**Response:** List of generated image paths with timestamps and prompts.

---

### clear_conversation

Reset the multi-turn chat session context.

**Parameters:** None

**Use when:** Starting a new creative direction, switching subjects, or when
the model's context from previous turns is interfering with new generations.

## Error Handling

| Error | Action |
|-------|--------|
| `MCP not configured` | Run `/banana setup` → `python3 setup_mcp.py` |
| `GOOGLE_AI_API_KEY invalid` | New key at https://aistudio.google.com/apikey |
| HTTP 429 (rate limited) | Wait 60s, retry with exponential backoff (free tier: ~5-15 RPM) |
| `IMAGE_SAFETY` | Rephrase prompt, suggest alternatives, retry with user approval (max 3) |
| `PROHIBITED_CONTENT` | Non-retryable -- explain and suggest alternative concepts |
| `FAILED_PRECONDITION` | Billing not enabled -- do not retry |
| MCP unavailable | Fall back to `scripts/generate.py` or `scripts/edit.py` |

## MCP Fallback (Direct API)

When MCP is unavailable, use the direct API scripts:

```bash
# Generate
python3 ${CLAUDE_SKILL_DIR}/scripts/generate.py \
  --prompt "your crafted prompt" \
  --aspect-ratio "16:9" \
  --resolution "2K"

# Edit
python3 ${CLAUDE_SKILL_DIR}/scripts/edit.py \
  --image "/path/to/image.png" \
  --prompt "your enhanced edit instruction"
```

Both scripts require `GOOGLE_AI_API_KEY` in environment or `--api-key` flag.
