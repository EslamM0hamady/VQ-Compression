## Vector Quantization Image Compression (Python + Tkinter GUI)

This project implements **grayscale image compression using Vector Quantization (VQ)** with the **Linde–Buzo–Gray (LBG) algorithm**, wrapped in a simple **Tkinter-based GUI**.  
Users can load an image, configure block and codebook parameters, run compression, and visualize both the original and reconstructed image along with basic quality metrics.

### Features

- **Interactive GUI** built with Tkinter
  - Load any image from disk (converted to grayscale automatically)
  - Configure:
    - Block height
    - Block width
    - Number of codevectors (codebook size)
  - View original and reconstructed images side by side
- **Vector Quantization Compression**
  - Image is divided into non-overlapping blocks
  - LBG algorithm is used to learn the codebook from block vectors
  - Each block is represented by the index of its nearest codevector
- **Quantitative Metrics**
  - **Compression ratio** (original size vs. compressed representation)
  - **Mean Squared Error (MSE)** between original and reconstructed images
- **File Outputs**
  - `codebook.txt`: learned codebook and padded image dimensions
  - `compressed.txt`: indices of the selected codevectors for all blocks
  - `reconstructed.png`: reconstructed image saved to disk

---

### Technical Overview

The core processing pipeline is:

1. **Block Partitioning**
   - The grayscale image is padded if necessary and divided into fixed-size blocks (e.g., 8×8).
   - Each block is flattened into a vector for further processing.

2. **Codebook Design (LBG Algorithm)**
   - Starts with a single vector: the mean of all block vectors.
   - Iteratively splits and refines the codebook using the LBG algorithm until the desired number of codevectors is reached.
   - Convergence is based on relative distortion change below a small threshold `epsilon`.

3. **Compression**
   - For each block vector, the nearest codevector (in Euclidean distance) is selected.
   - The compressed representation is just the list of indices plus the codebook.

4. **Decompression**
   - Indices are mapped back to their corresponding codevectors.
   - Block vectors are reshaped and stitched back to form the padded image.
   - The padded image is cropped back to the original size.

5. **Evaluation**
   - **Compression Ratio** is computed from:
     - Number of pixels in the original image
     - Size of the codebook
     - Number of block indices
   - **MSE** is computed between original and reconstructed images.

Key modules/functions:
- `divide_into_blocks(image, block_h, block_w)`
- `lbg_algorithm(blocks, num_codevectors, epsilon=1e-5)`
- `compress_image(blocks, codebook)`
- `decompress_image(indices, codebook, block_h, block_w, H, W)`
- `compression_ratio(original_image, indices, codebook)`
- `mean_squared_error(img1, img2)`
- `VQ_GUI`: main Tkinter GUI class

---

### Requirements

- **Python 3.8+** (recommended)
- Dependencies:
  - `numpy`
  - `Pillow` (PIL)
  - Standard library `tkinter` (usually bundled with Python on most systems)

You can install the required packages via:

pip install numpy pillow> Note: On some systems, `tkinter` may need to be installed separately (e.g., via your OS package manager).

---

### Getting Started

1. **Clone the repository**

git clone https://github.com/<your-username>/VQ-Compression-.git
cd VQ-Compression-2. **Create and activate a virtual environment (optional but recommended)**

python -m venv .venv
# Windows
.venv\Scripts\activate
# Linux / macOS
source .venv/bin/activate3. **Install dependencies**

pip install numpy pillow4. **Run the application**

python main.pyA window titled **"Vector Quantization Compression"** should appear.

---

### Usage

1. **Load an Image**
   - Click **"Load Image"**.
   - Choose any compatible image file (it will be converted to grayscale internally).
   - The original image appears on the left side of the GUI.

2. **Configure Parameters**
   - **Block Height / Block Width**
     - Default: `8 × 8`
     - Must be positive integers.
   - **Number of Codevectors**
     - Default: `256`
     - Must be a positive integer.
     - A higher number of codevectors generally improves quality but reduces compression.

3. **Compress & Reconstruct**
   - Click **"Compress & Reconstruct"**.
   - The application will:
     - Divide the image into blocks.
     - Run the LBG algorithm to generate a codebook.
     - Compress the image to indices.
     - Reconstruct the image from the compressed representation.
     - Display the reconstructed image on the right.
     - Save:
       - `codebook.txt`
       - `compressed.txt`
       - `reconstructed.png`
     - Show **Compression Ratio** and **MSE** in the info area.

---

### Output Files

- **`codebook.txt`**
  - First line: padded height and width (integers).
  - Remaining lines: codevectors (one per line, flattened block values).

- **`compressed.txt`**
  - Contains integer indices, one per line, each referring to a codevector in the codebook.

- **`reconstructed.png`**
  - The reconstructed grayscale image after decompression and cropping to original size.

These files can be used for further analysis or as part of a custom compression pipeline.

---

### Example

1. Load a grayscale or color image (color will be converted to grayscale).
2. Use the default parameters:
   - Block height: `8`
   - Block width: `8`
   - Number of codevectors: `256`
3. Click **"Compress & Reconstruct"**.
4. Observe:
   - Visual difference between original and reconstructed images.
   - Reported **Compression Ratio** (e.g., `2.5`) and **MSE** (e.g., `120.3`).

---

### Project Structure

- `main.py`  
  Main script containing:
  - VQ / LBG implementation
  - File I/O helpers for codebook and compressed indices
  - Tkinter-based GUI

(You can add more modules or a `requirements.txt` file as the project evolves.)

---

### Limitations & Notes

- Currently supports **grayscale images** (input images are converted to grayscale).
- Vector Quantization is applied on **non-overlapping blocks**, which can introduce blocking artifacts.
- Computation time increases with:
  - Image size
  - Decreasing block size
  - Increasing number of codevectors
- No explicit GPU support; computation is purely CPU-based via `numpy`.

---

### Possible Improvements

- Support for **color images** (e.g., compress each channel separately or in a color space like YCbCr).
- Add **progress indication** or a status bar during LBG training for large images.
- Command-line interface (CLI) mode for batch compression.
- Extended evaluation metrics: PSNR, SSIM, etc.
- Configurable save paths and more robust error handling.