# Sturdy-Octo-Disco-Adding-Sunglasses-for-a-Cool-New-Look

Sturdy Octo Disco is a fun project that adds sunglasses to photos using image processing.

Welcome to Sturdy Octo Disco, a fun and creative project designed to overlay sunglasses on individual passport photos! This repository demonstrates how to use image processing techniques to create a playful transformation, making ordinary photos look extraordinary. Whether you're a beginner exploring computer vision or just looking for a quirky project to try, this is for you!

## Features:
- Detects the face in an image.
- Places a stylish sunglass overlay perfectly on the face.
- Works seamlessly with individual passport-size photos.
- Customizable for different sunglasses styles or photo types.

## Technologies Used:
- Python
- OpenCV for image processing
- Numpy for array manipulations

## How to Use:
1. Clone this repository.
2. Add your passport-sized photo to the `images` folder.
3. Run the script to see your "cool" transformation!

## Applications:
- Learning basic image processing techniques.
- Adding flair to your photos for fun.
- Practicing computer vision workflows.

## Program:
---
import cv2

import matplotlib.pyplot as plt

img = cv2.imread("photo.png")

if img is None:
    raise FileNotFoundError("Photo not found!")

img_rgb = cv2.cvtColor(img, cv2.COLOR_BGR2RGB)

plt.figure(figsize=(5, 5))

plt.imshow(img_rgb)

plt.title("Step 1: Original Passport Size Photo")

plt.axis("off")

plt.show()

print("Image Height:", img.shape[0])

print("Image Width:", img.shape[1])

Feel free to fork, contribute, or customize this project for your creative needs!
---
## Output :
<img width="557" height="573" alt="image" src="https://github.com/user-attachments/assets/d02301c4-330e-4693-8ef7-3b59670c33b7" />
---
import cv2

import matplotlib.pyplot as plt

img = cv2.imread("photo.png")

if img is None:
    raise FileNotFoundError("Photo not found!")

height, width = img.shape[:2]

print("Image Height:", height)

print("Image Width:", width)

x = 530
y = 570
w = 260
h = 100

output = img.copy()

cv2.rectangle(
    output,
    (x, y),
    (x + w, y + h),
    (0, 255, 0),
    3
)

plt.figure(figsize=(12, 6))

plt.subplot(1, 2, 1)

plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))

plt.title("Original Image")

plt.axis("off")

plt.subplot(1, 2, 2)

plt.imshow(cv2.cvtColor(output, cv2.COLOR_BGR2RGB))

plt.title("Step 2: Eye Region")

plt.axis("off")

plt.show()

print("Eye Region Coordinates:")

print("X =", x)

print("Y =", y)

print("Width =", w)

print("Height =", h)
---
## Output :
<img width="1376" height="832" alt="image" src="https://github.com/user-attachments/assets/41d698a1-925e-4d12-97d7-d1f62845beb7" />
---
import cv2

import numpy as np

import matplotlib.pyplot as plt

img = cv2.imread("photo.png")

glass = cv2.imread("sunglasses.jpg", cv2.IMREAD_UNCHANGED)

if img is None or glass is None:
    raise FileNotFoundError("Check image filenames!")

x = 530
y = 570
w = 260
h = 100

glass = cv2.resize(glass, (w, h))

if glass.shape[2] == 4:

    # Sunglasses with transparent background
    glass_rgb = glass[:, :, :3]
    mask = glass[:, :, 3]

else:

    # Sunglasses JPG with white background
    glass_rgb = glass

    gray = cv2.cvtColor(
        glass_rgb,
        cv2.COLOR_BGR2GRAY
    )

    _, mask = cv2.threshold(
        gray, 240, 255,
        cv2.THRESH_BINARY_INV
    )

mask_inv = cv2.bitwise_not(mask)

roi = img[y:y+h, x:x+w]

if roi.shape[:2] != (h, w):
    raise ValueError("Eye region exceeds image boundaries!")

glass_fg = cv2.bitwise_and(
    glass_rgb,
    glass_rgb,
    mask=mask
)

img_bg = cv2.bitwise_and(
    roi,
    roi,
    mask=mask_inv
)

combined = cv2.add(img_bg, glass_fg)

result = img.copy()

result[y:y+h, x:x+w] = combined

plt.figure(figsize=(15, 5))

plt.subplot(1, 3, 1)

plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))

plt.title("Original Image")

plt.axis("off")

plt.subplot(1, 3, 2)

plt.imshow(mask, cmap="gray")

plt.title("Sunglasses Mask")

plt.axis("off")

plt.subplot(1, 3, 3)

plt.imshow(cv2.cvtColor(result, cv2.COLOR_BGR2RGB))

plt.title("Step 3: Sunglasses Overlay")

plt.axis("off")

plt.show()

cv2.imwrite("step3_sunglasses.jpg", result)

print("Step 3 completed successfully!")
---
## Output : 
<img width="1380" height="532" alt="image" src="https://github.com/user-attachments/assets/637570e3-52aa-425f-938a-1fa7ee13db39" />
---
import cv2

import numpy as np

import matplotlib.pyplot as plt

img = cv2.imread("photo.png")

glass = cv2.imread("sunglasses.jpg", cv2.IMREAD_UNCHANGED)

if img is None or glass is None:
    raise FileNotFoundError("Check image filenames!")

x = 530
y = 570
w = 260
h = 100

glass = cv2.resize(glass, (w, h))

if glass.shape[2] == 4:

    glass_rgb = glass[:, :, :3]

    alpha = glass[:, :, 3].astype(float) / 255.0

else:

    glass_rgb = glass

    gray = cv2.cvtColor(
        glass_rgb,
        cv2.COLOR_BGR2GRAY
    )

    _, mask = cv2.threshold(
        gray,
        240,
        255,
        cv2.THRESH_BINARY_INV
    )

    # Smooth edges
    mask = cv2.GaussianBlur(
        mask,
        (5, 5),
        0
    )

    alpha = mask.astype(float) / 255.0

alpha = alpha[:, :, np.newaxis]

roi = img[y:y+h, x:x+w]

if roi.shape[:2] != (h, w):
    raise ValueError("Eye region exceeds image boundaries!")

foreground = glass_rgb.astype(float) * alpha

background = roi.astype(float) * (1 - alpha)

blended = foreground + background

blended = np.clip(
    blended,
    0,
    255
).astype(np.uint8)

result = img.copy()

result[y:y+h, x:x+w] = blended

plt.figure(figsize=(15, 5))

plt.subplot(1, 3, 1)

plt.imshow(cv2.cvtColor(img, cv2.COLOR_BGR2RGB))

plt.title("Original Image")

plt.axis("off")

plt.subplot(1, 3, 2)

plt.imshow(alpha[:, :, 0], cmap="gray")

plt.title("Alpha Mask")

plt.axis("off")

plt.subplot(1, 3, 3)

plt.imshow(cv2.cvtColor(result, cv2.COLOR_BGR2RGB))

plt.title("Step 4: Final Sunglasses Overlay")

plt.axis("off")

plt.show()

cv2.imwrite("final_sunglasses.jpg", result)

print("Step 4 completed successfully!")

print("Final image saved as final_sunglasses.jpg")
---
## Output :
<img width="1413" height="550" alt="image" src="https://github.com/user-attachments/assets/e771e081-d3cb-49ff-b8a3-0e5578fe41c4" />



