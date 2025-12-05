# NanoBanana Image Generation MCP Server

Build an MCP server that generates images using Google's Gemini image generation API (also known as NanoBanana).

## Overview

This MCP server demonstrates:
- External API integration with Google's Gemini API
- Async operations for image generation
- File handling and saving generated images
- Enhancing visual workflows in your projects

## Why Image Generation in MCP?

When building applications like the Art Gallery, having an AI that can generate images on demand is powerful:
- Generate placeholder artwork during development
- Create variations of existing designs
- Produce visual assets directly from descriptions
- Iterate quickly on visual concepts

## Prerequisites

- Conda environment activated (`mcp-workshop`)
- Google AI API key (free tier available)

## Step 1: Get a Google AI API Key

1. Visit [Google AI Studio](https://aistudio.google.com/apikey)
2. Click "Create API key"
3. Select or create a Google Cloud project
4. Copy your API key

## Step 2: Create Project Structure

```bash
# From the workshop repo root
mkdir -p mcp-servers/nanobanana
cd mcp-servers/nanobanana
```

## Step 3: Install Dependencies

```bash
conda activate mcp-workshop
pip install google-genai python-dotenv pillow
```

- **google-genai**: Google's Generative AI Python SDK
- **python-dotenv**: Load environment variables from `.env` file
- **pillow**: Image handling library

## Step 4: Configure API Key

Create a `.env` file in the `mcp-servers/nanobanana` folder:

```bash
cat > .env << 'EOF'
GEMINI_API_KEY=your_api_key_here
EOF
```

**Important:** Replace `your_api_key_here` with your actual Gemini API key from Google AI Studio.

## Step 5: Create the Server

Create a file called `server.py`:

```python
import os
import mimetypes
import time
from pathlib import Path
from dotenv import load_dotenv
from google import genai
from google.genai import types
from fastmcp import FastMCP

# Load environment variables from .env file
load_dotenv()

# Create the MCP server
mcp = FastMCP("Image Generation")

# Configure the Google AI client
client = genai.Client(api_key=os.getenv("GEMINI_API_KEY"))

# Default output directory for generated images
OUTPUT_DIR = Path(os.getenv("IMAGE_OUTPUT_DIR", "./generated_images"))
OUTPUT_DIR.mkdir(parents=True, exist_ok=True)


def _generate_image_internal(prompt: str, filename: str = None, image_size: str = "1K") -> dict:
    """Internal function to generate an image."""
    try:
        # Build the request using proper types
        contents = [
            types.Content(
                role="user",
                parts=[types.Part.from_text(text=prompt)],
            ),
        ]

        generate_content_config = types.GenerateContentConfig(
            response_modalities=["IMAGE", "TEXT"],
            image_config=types.ImageConfig(image_size=image_size),
        )

        # Generate using streaming API
        for chunk in client.models.generate_content_stream(
            model="gemini-3-pro-image-preview",
            contents=contents,
            config=generate_content_config,
        ):
            if (
                chunk.candidates is None
                or chunk.candidates[0].content is None
                or chunk.candidates[0].content.parts is None
            ):
                continue

            part = chunk.candidates[0].content.parts[0]
            if part.inline_data and part.inline_data.data:
                # Generate filename if not provided
                if not filename:
                    filename = f"image_{int(time.time())}"

                # Get file extension from mime type
                file_ext = mimetypes.guess_extension(part.inline_data.mime_type) or ".png"
                filepath = OUTPUT_DIR / f"{filename}{file_ext}"

                # Save the image data directly (already binary)
                with open(filepath, "wb") as f:
                    f.write(part.inline_data.data)

                return {
                    "success": True,
                    "message": "Image generated successfully",
                    "filepath": str(filepath.absolute()),
                    "prompt": prompt,
                }

        return {"success": False, "error": "No image was generated in the response"}

    except Exception as e:
        return {"success": False, "error": str(e)}


@mcp.tool
def generate_image(prompt: str, filename: str = None, image_size: str = "1K") -> dict:
    """Generate an image from a text description using NanoBanana (Gemini).

    Args:
        prompt: A detailed description of the image to generate
        filename: Optional filename for the image (without extension).
                  If not provided, uses a timestamp.
        image_size: Output resolution - '1K' (default) or '4K' for higher detail
    """
    return _generate_image_internal(prompt, filename, image_size)


# Style presets with optimized prompt fragments
STYLE_PRESETS = {
    # Painting styles
    "watercolor": "soft watercolor painting with gentle ink wash outlines, painterly edges, visible brush strokes, pastel tones blending naturally",
    "oil_painting": "classical oil painting with rich impasto texture, visible brush strokes, deep saturated colors, canvas texture visible",
    "impressionist": "impressionist painting style with loose brush strokes, dappled light effects, vibrant color patches, atmospheric depth",
    "abstract": "abstract expressionist artwork with bold geometric shapes, dynamic composition, non-representational forms, striking color contrasts",

    # Digital & illustration styles
    "digital_art": "polished digital artwork with clean lines, smooth gradients, professional illustration quality, vibrant colors",
    "vector": "flat vector illustration with clean bold outlines, smooth shapes, solid flat colors, no gradients or shading, minimalist design",
    "concept_art": "professional concept art with cinematic composition, atmospheric perspective, detailed environment design, dramatic lighting",

    # Anime & cartoon styles
    "anime": "anime illustration style with large expressive eyes, clean cel-shading, vibrant colors, dynamic pose, manga-influenced aesthetics",
    "chibi": "chibi style with super-deformed proportions, oversized head, tiny body, cute simplified features, kawaii aesthetic",
    "pixel_art": "retro pixel art with limited color palette, visible pixels, dithering for shadows, nostalgic 8-bit aesthetic",

    # Realistic styles
    "photorealistic": "photorealistic rendering with accurate lighting, natural textures, high detail, cinematic quality",
    "3d_render": "polished 3D render with subsurface scattering, realistic materials, studio lighting, high-fidelity CGI quality",
}

LIGHTING_PRESETS = {
    "natural": "soft natural daylight, even illumination",
    "dramatic": "dramatic chiaroscuro lighting with deep shadows and bright highlights",
    "golden_hour": "warm golden hour sunlight, long shadows, amber tones",
    "studio": "professional studio lighting, soft shadows, even exposure",
    "neon": "vibrant neon lighting with glowing colors, cyberpunk atmosphere",
    "moonlit": "cool moonlight ambiance, soft blue tones, mysterious atmosphere",
}

COMPOSITION_PRESETS = {
    "centered": "centered composition with subject as focal point",
    "rule_of_thirds": "rule of thirds composition with balanced visual weight",
    "dynamic": "dynamic diagonal composition with sense of movement",
    "close_up": "intimate close-up framing highlighting details",
    "wide_shot": "expansive wide shot showing full environment context",
}

ASPECT_RATIO_PRESETS = {
    "square": "1:1 aspect ratio, square format",
    "portrait": "3:4 aspect ratio, vertical portrait orientation",
    "landscape": "4:3 aspect ratio, horizontal landscape orientation",
    "wide": "16:9 aspect ratio, cinematic widescreen format",
    "ultrawide": "2:1 aspect ratio, panoramic ultrawide format",
}


@mcp.tool
def generate_artwork(
    subject: str,
    style: str = "digital_art",
    mood: str = "vibrant",
    lighting: str = "natural",
    composition: str = "centered",
    aspect_ratio: str = "square",
    color_palette: str = None,
    image_size: str = "1K",
    filename: str = None
) -> dict:
    """Generate artwork with detailed artistic parameters using optimized prompting.

    Args:
        subject: The main subject of the artwork (e.g., 'a majestic dragon', 'peaceful garden')
        style: Art style preset. Options: 'watercolor', 'oil_painting', 'impressionist',
               'abstract', 'digital_art', 'vector', 'concept_art', 'anime', 'chibi',
               'pixel_art', 'photorealistic', '3d_render'
        mood: Emotional atmosphere (e.g., 'vibrant', 'serene', 'dramatic', 'whimsical',
              'melancholic', 'energetic', 'mysterious')
        lighting: Lighting style. Options: 'natural', 'dramatic', 'golden_hour',
                  'studio', 'neon', 'moonlit'
        composition: Framing approach. Options: 'centered', 'rule_of_thirds',
                     'dynamic', 'close_up', 'wide_shot'
        aspect_ratio: Image dimensions. Options: 'square' (1:1), 'portrait' (3:4),
                      'landscape' (4:3), 'wide' (16:9), 'ultrawide' (2:1)
        color_palette: Optional color scheme (e.g., 'warm earth tones', 'cool blues',
                       'monochromatic', 'complementary orange and teal')
        image_size: Output resolution - '1K' (default) or '4K' for higher detail
        filename: Optional filename for the image (without extension)
    """
    # Build the structured prompt from parameters
    style_desc = STYLE_PRESETS.get(style, style)
    lighting_desc = LIGHTING_PRESETS.get(lighting, lighting)
    composition_desc = COMPOSITION_PRESETS.get(composition, composition)
    aspect_desc = ASPECT_RATIO_PRESETS.get(aspect_ratio, aspect_ratio)

    # Construct optimized prompt using best practices from research
    prompt_parts = [
        f"Create a {mood} artwork depicting {subject}.",
        f"Style: {style_desc}.",
        f"Lighting: {lighting_desc}.",
        f"Composition: {composition_desc}.",
        f"Format: {aspect_desc}.",
    ]

    if color_palette:
        prompt_parts.append(f"Color palette: {color_palette}.")

    prompt_parts.append("High quality, sharp details, professional artwork.")

    prompt = " ".join(prompt_parts)

    return _generate_image_internal(prompt, filename, image_size)


@mcp.tool
def list_generated_images() -> list[dict]:
    """List all previously generated images."""
    images = []
    for filepath in OUTPUT_DIR.glob("*.png"):
        images.append({
            "filename": filepath.name,
            "filepath": str(filepath.absolute()),
            "size_kb": round(filepath.stat().st_size / 1024, 2),
        })
    return sorted(images, key=lambda x: x["filename"], reverse=True)


if __name__ == "__main__":
    mcp.run(transport="http", host="127.0.0.1", port=8003)
```

### Understanding the Code

**API Configuration:**
- Uses `google-genai` SDK for Gemini API access
- API key loaded from environment variable
- Output directory created automatically

**Image Generation Tools:**
- `generate_image`: Direct text-to-image generation with any prompt
- `generate_artwork`: Structured artwork generation with style presets and artistic parameters
- `list_generated_images`: View previously generated images

**Style Presets (based on NanoBanana JSON prompting research):**
- **Painting styles**: `watercolor`, `oil_painting`, `impressionist`, `abstract`
- **Digital styles**: `digital_art`, `vector`, `concept_art`
- **Anime/cartoon**: `anime`, `chibi`, `pixel_art`
- **Realistic**: `photorealistic`, `3d_render`

**Lighting, Composition & Format Presets:**
- Lighting: `natural`, `dramatic`, `golden_hour`, `studio`, `neon`, `moonlit`
- Composition: `centered`, `rule_of_thirds`, `dynamic`, `close_up`, `wide_shot`
- Aspect Ratio: `square` (1:1), `portrait` (3:4), `landscape` (4:3), `wide` (16:9), `ultrawide` (2:1)
- Image Size: `1K` (default), `4K` (higher resolution)

The presets translate user-friendly parameters into optimized prompt fragments internally, applying best practices from NanoBanana JSON prompting without exposing the complexity.

**File Handling:**
- Images saved as PNG files in the `generated_images` folder
- Timestamps used for unique filenames if not specified
- Returns absolute file paths for easy access

## Step 6: Run the Server

```bash
conda activate mcp-workshop
cd mcp-servers/nanobanana
python server.py
```

You should see the server running on `http://127.0.0.1:8003`. Keep this terminal open.

## Step 7: Add to Claude Code

Update the `.mcp.json` in your art-gallery project to include NanoBanana:

```json
{
  "mcpServers": {
    "users-local-datetime-server": {
      "type": "http",
      "url": "http://127.0.0.1:8001/mcp"
    },
    "task-tracker": {
      "type": "http",
      "url": "http://127.0.0.1:8002/mcp"
    },
    "image-generation": {
      "type": "http",
      "url": "http://127.0.0.1:8003/mcp",
      "_comment": "Generate images using Google Gemini (NanoBanana)"
    }
  }
}
```

## Step 8: Verify MCP Connection

Launch Claude Code from your art-gallery folder:

```bash
conda activate mcp-workshop
cd art-gallery
claude
```

Verify the server is connected:

```
/mcp
```

You should see `image-generation` listed among your configured MCP servers.

## Step 9: Test Image Generation

Try these prompts in Claude Code to test the different style presets:

**Watercolor painting:**
```
Generate artwork of a cherry blossom tree in watercolor style with serene mood,
golden_hour lighting, and soft pink color palette. Show me the result.
```

**Anime style:**
```
Create an anime artwork of a magical forest spirit with dramatic lighting
and dynamic composition. Display the image.
```

**Abstract art (4K):**
```
Generate an abstract piece depicting the concept of "time" with vibrant mood,
complementary blue and orange color palette, 4K image size. Save as "time_abstract"
```

**Concept art (widescreen):**
```
Create concept art of an ancient floating temple with mysterious mood,
moonlit lighting, wide_shot composition, wide aspect ratio. Show me the image.
```

**Simple prompt (uses defaults):**
```
Generate artwork of a cozy coffee shop
```

**List generated images:**
```
List all the images that have been generated
```

### How Claude Views Generated Images

When NanoBanana generates an image, it returns the file path. Claude Code can then read and display the image using its built-in Read tool (Claude is multimodal and can view images).

The workflow:
1. You ask Claude to generate an image
2. NanoBanana creates the image and returns `{"filepath": "/absolute/path/to/image.png"}`
3. Claude uses the Read tool to view the image and describe it to you
4. You can copy the image to your project as needed

This keeps the MCP server independent - it works with any project, not just art-gallery.

---

## How It Works

```
┌─────────────┐   HTTP (8003)    ┌──────────────┐   HTTPS    ┌────────────┐
│ Claude Code │ ◄──────────────► │  NanoBanana  │ ◄────────► │ Google AI  │
│   (Client)  │                  │  (FastMCP)   │            │ Gemini API │
└─────────────┘                  └──────────────┘            └────────────┘
       │                                │                          │
       │  "Generate abstract art..."    │                          │
       │                                │                          │
       │  ─► generate_artwork() ──────► │ ─► API Request ────────► │
       │                                │                          │
       │                                │ ◄── Image Data ◄──────── │
       │                                │                          │
       │  ◄── {filepath: "..."} ◄────── │    (saves to disk)       │
       │                                │                          │
       │  "Created image at..."         │                          │
       ▼                                ▼                          ▼
```

---

## Use with Art Gallery Project

Generate artwork for your Art Gallery carousel:

```
Generate 3 abstract artworks for the Art Gallery project:
1. A vibrant abstract piece with geometric shapes - save as "geometric"
2. A serene landscape in impressionist style - save as "landscape"
3. A dramatic digital art piece with cosmic themes - save as "cosmic"

Show me each image after generating it.
```

Once you're happy with the generated images, copy them to your art gallery:

```bash
# Copy all generated images to art-gallery
cp mcp-servers/nanobanana/generated_images/*.png art-gallery/public/artwork/

# Or copy specific images
cp mcp-servers/nanobanana/generated_images/geometric.png art-gallery/public/artwork/
```

This workflow lets you:
- Generate and preview multiple variations
- Keep only the images you like
- Maintain a clean separation between the MCP server and your project

---

## Troubleshooting

**API key errors:**
- Verify your API key is correct in the `.env` file
- Check that you have the Gemini API enabled in Google Cloud Console
- Ensure you haven't exceeded the free tier limits

**No image generated:**
- Some prompts may be filtered for safety - try rephrasing
- Check the API response for specific error messages

**Import errors:**
- Ensure all dependencies are installed: `pip install google-genai python-dotenv pillow`
- Verify you're in the correct conda environment

**Server already running on port 8003:**
- Change the port in `server.py` and update `.mcp.json`

---

## Key Concepts Learned

1. **External API Integration**: Connecting to third-party APIs from MCP servers
2. **Async Operations**: Handling API calls that may take several seconds
3. **File Handling**: Saving binary data (images) to disk
4. **Structured Prompts**: Building effective prompts from parameters
5. **Error Handling**: Gracefully handling API failures

---

## Extending the Server

Try adding more tools:

```python
@mcp.tool
def edit_image(image_path: str, edit_instructions: str, filename: str = None) -> dict:
    """Edit an existing image with natural language instructions.

    Args:
        image_path: Path to the image to edit
        edit_instructions: Description of the changes to make
        filename: Optional filename for the edited image
    """
    # Load the image
    with open(image_path, "rb") as f:
        image_data = base64.b64encode(f.read()).decode()

    # Send to Gemini for editing
    response = client.models.generate_content(
        model="gemini-2.0-flash-exp",
        contents=[
            {"text": edit_instructions},
            {"inline_data": {"mime_type": "image/png", "data": image_data}}
        ],
        config={"response_modalities": ["TEXT", "IMAGE"]},
    )
    # ... save and return result
```

---

## Resources

**API & SDK:**
- [Google AI Studio](https://aistudio.google.com/) - Get API keys and test prompts
- [Gemini API Documentation](https://ai.google.dev/gemini-api/docs/image-generation)
- [google-genai Python SDK](https://github.com/google/generative-ai-python)

**NanoBanana Prompting Research:**
- [JSON Prompts for Gemini NanoBanana Guide](https://www.godofprompt.ai/blog/write-json-prompts-for-gemini-nano-banana) - Structured prompting techniques
- [23 Cartoon Style Prompts](https://imaginewithrashid.com/23-gemini-nano-banana-pro-prompts-for-different-cartoon-styles/) - Style-specific prompt examples
- [NanoBanana Pro Prompting Guide](https://dev.to/googleai/nano-banana-pro-prompting-guide-strategies-1h9n) - Official strategies
- [awesome-nano-banana](https://github.com/JimmyLv/awesome-nano-banana) - Community prompt collection
