# Brand/Style Presets Reference -- Banana Claude

> Load this on-demand when user mentions a brand preset or runs `/banana preset`.

## Preset Schema

Each preset stored at `~/.banana/presets/NAME.json`:

```json
{
  "name": "tech-saas",
  "description": "Clean SaaS product aesthetic",
  "colors": ["#2563EB", "#1E40AF", "#F8FAFC"],
  "style": "clean minimal tech illustration, flat vectors, soft shadows",
  "typography": "geometric sans-serif, bold headings",
  "lighting": "soft diffused, cool blue tones",
  "mood": "professional, modern, trustworthy",
  "default_ratio": "16:9",
  "default_resolution": "2K"
}
```

## Built-in Preset Examples

### tech-saas
- **Colors:** Blue palette (#2563EB, #1E40AF, #F8FAFC)
- **Style:** Clean minimal tech illustration, flat vectors, soft shadows
- **Typography:** Geometric sans-serif
- **Mood:** Professional, modern

### luxury-brand
- **Colors:** Dark sophisticated (#1A1A1A, #C9A96E, #FAFAF5)
- **Style:** Elegant high-end photography, rich textures, deep contrast
- **Typography:** Thin serif fonts
- **Mood:** Exclusive, aspirational

### editorial-magazine
- **Colors:** High-contrast black, white, red
- **Style:** Bold editorial photography, strong geometric composition
- **Typography:** Condensed headlines
- **Mood:** Bold, contemporary

## How Presets Work

When a preset is active, its values become defaults in the Reasoning Brief:
- `colors` → palette description in Style component
- `style` → visual direction baseline
- `typography` → text rendering guidance
- `lighting` → lighting environment baseline
- `mood` → overall tone modifier

**User instructions always override preset values.**

## Preset Management Commands

```bash
# List all presets
python3 ${CLAUDE_SKILL_DIR}/scripts/presets.py list

# Show preset details
python3 ${CLAUDE_SKILL_DIR}/scripts/presets.py show tech-saas

# Create new preset
python3 ${CLAUDE_SKILL_DIR}/scripts/presets.py create my-brand \
  --colors "#FF6B35,#1A1A2E" \
  --style "bold vibrant lifestyle photography" \
  --mood "energetic, youthful" \
  --description "My brand visual identity"

# Delete preset
python3 ${CLAUDE_SKILL_DIR}/scripts/presets.py delete my-brand --confirm
```

## Preset Merge Behavior

When generating with a preset, merge priority (highest first):
1. Explicit user instruction ("use red background")
2. Preset values
3. Skill defaults

Never silently override explicit user requests with preset values.
