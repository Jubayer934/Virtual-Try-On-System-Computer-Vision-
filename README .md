# 👕 Virtual Try-On System

A Colab notebook that lets you upload a photo, pick **Upper Body** or **Lower Body**, describe new clothing in plain text, and generates a version of the photo wearing that clothing — with a segmentation mask shown alongside as visual proof of what region was targeted.

## How it works
1. **Segmentation** — [`mattmdjaga/segformer_b2_clothes`](https://huggingface.co/mattmdjaga/segformer_b2_clothes) (SegFormer, via Hugging Face `transformers`) segments the uploaded photo into body/clothing regions and builds a mask for the chosen area:
   - **Upper Body** → Upper-clothes + Dress classes
   - **Lower Body** → Skirt + Pants classes
2. **Generation** — the photo and a text instruction (e.g. *"Change only the upper body clothing to: black leather jacket. Keep the person's face, pose, body shape, and background exactly the same."*) are sent to **FLUX.1-Kontext-dev** through the Hugging Face Inference API (`fal-ai` provider) to produce the edited image.
3. **UI** — a Gradio Blocks interface ties it together: upload → select body part → describe clothing → click **Try On** → view the result and the segmentation mask.

## Run it
1. Open `Virtual_Try-On_System.ipynb` in Google Colab.
2. Run all cells top to bottom (GPU runtime recommended, but CPU also works — just slower).
3. Use the Gradio link/embed that appears at the bottom to upload a photo and try it out.

## Requirements
Installed automatically in the first cell:
```
gradio
huggingface_hub
transformers
torch
torchvision
pillow
```

## ⚠️ Security note
The notebook currently has a Hugging Face API token **hardcoded** in the inpainting cell. Since this notebook has been shared, that token should be treated as compromised — **revoke/regenerate it** at [huggingface.co/settings/tokens](https://huggingface.co/settings/tokens). Going forward, load it from an environment variable or Colab secret instead, e.g.:
```python
from google.colab import userdata
HF_TOKEN = userdata.get("HF_TOKEN")
```

## Example
| Input | Upper body try-on | Lower body try-on |
|---|---|---|
| Uploaded photo | New jacket | New jeans |

## Notes
- Segmentation runs locally (CPU/GPU in Colab); the actual clothing generation is done via an external API call, so it requires internet access and a valid Hugging Face token with Inference Providers access.
- Output quality/consistency (face, pose, background) depends on the FLUX.1-Kontext-dev model's response to the edit instruction — results can vary between runs.
