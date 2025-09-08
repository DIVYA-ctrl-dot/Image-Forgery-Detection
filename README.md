# Image-Forgery-Detection
Detect tampered (copied-pasted or spliced) regions in an image using error level analysis or block matching.
from PIL import Image, ImageChops, ImageEnhance
import matplotlib.pyplot as plt

def ela_image(path, quality=90):
    """Perform Error Level Analysis on an image."""
    original = Image.open(path).convert('RGB')

    # Save at lower quality
    resaved_path = "temp_resaved.jpg"
    original.save(resaved_path, 'JPEG', quality=quality)
    resaved = Image.open(resaved_path)

    # Compute difference
    diff = ImageChops.difference(original, resaved)

    # Enhance differences for visibility
    extrema = diff.getextrema()
    max_diff = max([ex[1] for ex in extrema])
    scale = 255.0 / max_diff if max_diff != 0 else 1
    diff = ImageEnhance.Brightness(diff).enhance(scale)

    return original, diff

# ----------- Run -----------
image_path = "test_image.jpg"   # <-- put your image here
orig, ela = ela_image(image_path, quality=90)

# Show results
plt.figure(figsize=(10,5))
plt.subplot(1,2,1)
plt.title("Original Image")
plt.imshow(orig)
plt.axis("off")

plt.subplot(1,2,2)
plt.title("ELA Result")
plt.imshow(ela)
plt.axis("off")

plt.show()
