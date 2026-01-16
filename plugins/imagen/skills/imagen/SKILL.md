---
name: imagen
description: |
  Generate images using Google Gemini Imagen. Supports text-to-image,
  image-to-image transformations, and reusable presets for consistent style.
---

# Gemini Imagen Skill

Generate high-quality images using Google Gemini's Imagen model with optional presets.

## Script Location

The scripts are in the skill's `scripts/` directory. Use the skill's base directory path provided when the skill is loaded.

## Usage

### Text-to-Image (basic)

```bash
python3 $SKILL_DIR/scripts/generate_with_preset.py "your prompt" output.jpg
```

### Text-to-Image with Presets

```bash
python3 $SKILL_DIR/scripts/generate_with_preset.py \
  --preset <preset_name> \
  "your prompt" \
  output.jpg
```

### Image-to-Image (edit/transform)

```bash
python3 $SKILL_DIR/scripts/generate_with_preset.py \
  --input source_image.jpg \
  "transformation instructions" \
  output.jpg
```

### Combined (input image + preset)

```bash
python3 $SKILL_DIR/scripts/generate_with_preset.py \
  --input source.jpg \
  --preset <preset_name> \
  "instructions" \
  output.jpg
```

## Options

| Option | Description |
|--------|-------------|
| `--preset, -p` | Preset name(s), comma-separated (e.g., `mockup,damemano`) |
| `--input, -i` | Input image(s) for image-to-image (can use multiple times) |
| `--size` | Output size: `512`, `1K` (default), or `2K` |
| `--remove-bg, -r` | Remove background (requires: `pip install rembg`) |
| `--output-svg, -s` | Convert to SVG (requires: `pip install vtracer`) |
| `--svg-mode` | SVG color mode: `color` (default) or `binary` for B/W |
| `--list, -l` | List available presets |
| `--show-prompt` | Show full prompt without generating |

## Discovering Presets

List available presets for the current project:

```bash
python3 $SKILL_DIR/scripts/generate_with_preset.py --list
```

Presets are searched in order:
1. `./presets/` (current project)
2. `./design/presets/` (current project)
3. `$IMAGEN_PRESETS_DIR` environment variable
4. Built-in presets in the skill directory

## Built-in Presets

| Preset | Purpose |
|--------|---------|
| `creative` | Brainstorming mode - bold, distinctive, non-generic designs |
| `mockup` | Mobile UI mockups - front view, no embellishments, 9:19.5 ratio |

## Common Tasks

### Generate UI mockup

```bash
python3 $SKILL_DIR/scripts/generate_with_preset.py \
  --preset mockup \
  "Login screen with email input, password field, and submit button" \
  login.jpg
```

### Apply project style to mockup

```bash
python3 $SKILL_DIR/scripts/generate_with_preset.py \
  --preset mockup,<project_preset> \
  "Search results page" \
  search.jpg
```

### Extract element from image

```bash
python3 $SKILL_DIR/scripts/generate_with_preset.py \
  --input mockup.jpg \
  "Extract the logo on transparent background" \
  logo.png
```

### Creative exploration

```bash
python3 $SKILL_DIR/scripts/generate_with_preset.py \
  --preset creative \
  "Landing page concepts for a food delivery app" \
  concepts.jpg
```

### Remove background

```bash
python3 $SKILL_DIR/scripts/generate_with_preset.py \
  --remove-bg \
  "app icon" \
  icon.png
```

### Generate SVG logo/icon

```bash
# Color SVG (full color vectorization)
python3 $SKILL_DIR/scripts/generate_with_preset.py \
  --output-svg \
  "minimalist app logo" \
  logo.svg

# Binary SVG for line art (faster, cleaner)
python3 $SKILL_DIR/scripts/generate_with_preset.py \
  --output-svg --svg-mode binary \
  "simple line icon" \
  icon.svg

# Combine with background removal for best results
python3 $SKILL_DIR/scripts/generate_with_preset.py \
  --remove-bg --output-svg \
  "logo on transparent background" \
  logo.svg
```

### Convert existing image to SVG

```bash
python3 $SKILL_DIR/scripts/convert_to_svg.py input.png output.svg

# Binary mode for line art
python3 $SKILL_DIR/scripts/convert_to_svg.py --mode binary icon.png icon.svg

# Logo preset (quantizes colors for cleaner output)
python3 $SKILL_DIR/scripts/convert_to_svg.py --preset logo input.png output.svg

# Project-specific palette for best results
python3 $SKILL_DIR/scripts/convert_to_svg.py --palette damemano logo.png logo.svg
```

**Tip:** For logos with gradients or anti-aliasing, use `--preset logo` or `--palette` to quantize colors first. This produces much cleaner SVGs.

## Parallel Generation

When generating multiple images, run commands in parallel for efficiency:

```bash
# Run these in parallel (multiple Bash calls in same response)
python3 $SKILL_DIR/scripts/generate_with_preset.py --preset mockup "home screen" home.jpg
python3 $SKILL_DIR/scripts/generate_with_preset.py --preset mockup "profile screen" profile.jpg
python3 $SKILL_DIR/scripts/generate_with_preset.py --preset mockup "settings screen" settings.jpg
```

## Environment

Requires `GEMINI_API_KEY` environment variable to be set.

## Notes

- The script auto-detects image format via magic bytes and corrects file extensions
- Output directory is created automatically if it doesn't exist
- For project-specific presets, create a `presets/` or `design/presets/` folder in the project root
