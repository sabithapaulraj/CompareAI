# CompareAI - Semantic PDF Comparison Tool

### Welcome to the CompareAI Repository!

CompareAI is a Python-based tool that allows you to compare two PDF documents and identify the semantic changes between them. It uses the sentence-transformers library to extract sentence embeddings and perform a detailed comparison, highlighting the differences in the output PDF file.

### Features
- **PDF Parsing**: Extract text from PDF files using PyMuPDF (fitz).
- **Text Embeddings**: Generate text embeddings using the sentence-transformers library.
- **Semantic Comparison**: Compare documents semantically to identify changes.
- **Highlight Changes**: Highlight the differences in the output PDF file.

### Progress So Far
- **Core Functionality Implemented**:
  - PDF text extraction is fully functional using PyMuPDF.
  - Sentence embedding generation using the sentence-transformers library is operational.
  - Semantic comparison of documents is implemented and tested.
  - Highlighting of changes in the output PDF is working seamlessly.

- **Optimizations**:
  - Efficient text extraction and processing.
  - Robust comparison mechanism to ensure accurate detection of changes.

### Code Functionality
The project’s main functionalities include:

1. **Extracting Text from PDFs**:
   - Load a PDF file and extract text content using PyMuPDF.
   - Example:
     ```python
     import fitz

     def read_pdf(file_path):
         doc = fitz.open(file_path)
         text_positions = []
         for page_num, page in enumerate(doc):
             blocks = page.get_text("blocks")
             for block in blocks:
                 text = block[4].strip()
                 if text:  # Ensure text is not empty
                     bbox = block[:4]
                     text_positions.append((text, bbox, page_num))
         return text_positions
     ```

2. **Generating Text Embeddings**:
   - Convert extracted text into embeddings using the sentence-transformers library.
   - Example:
     ```python
     from sentence_transformers import SentenceTransformer

     # Initialize the sentence transformer model
     model = SentenceTransformer('all-mpnet-base-v2')

     def get_embeddings(sentences):
         return model.encode([text for text, _, _ in sentences], convert_to_tensor=True)
     ```

3. **Comparing Documents Semantically**:
   - Compare two sets of text embeddings to find the differences.
   - Example:
     ```python
     import torch
     from sentence_transformers import util

     def compare_documents_semantically(old_pdf, new_pdf, threshold=0.8):
         old_text = read_pdf(old_pdf)
         new_text = read_pdf(new_pdf)

         old_embeddings = get_embeddings(old_text)
         new_embeddings = get_embeddings(new_text)

         diff = []
         for i, (new_sentence, bbox, page_num) in enumerate(new_text):
             new_embedding = new_embeddings[i]
             similarities = util.pytorch_cos_sim(new_embedding, old_embeddings)
             max_similarity = torch.max(similarities).item()
             if max_similarity < threshold:
                 diff.append((new_sentence, bbox, page_num))

         return diff
     ```

4. **Highlighting Changes in the PDF**:
   - Highlight the differences in the new PDF file.
   - Example:
     ```python
     import fitz

     def highlight_changes_semantically(diff, output_path, new_pdf):
         doc = fitz.open(new_pdf)
         for line, bbox, page_num in diff:
             page = doc[page_num]
             highlight = page.add_highlight_annot(bbox)
             highlight.set_colors(stroke=(1, 1, 0))  # Yellow highlight
             highlight.update()
         doc.save(output_path)
     ```

### Setup and Installation
1. Clone the repository:
   ```
   git clone https://github.com/your-username/compareAI.git
   ```
2. Create a virtual environment and install the required dependencies:
   ```
   cd compareAI python -m venv venv source venv/bin/activate pip install -r requirements.txt
   ```

3. Upload the old and new PDF files for comparison:
```python
from google.colab import files

uploaded_old = files.upload()
uploaded_new = files.upload()

old_pdf_path = list(uploaded_old.keys())[0]
new_pdf_path = list(uploaded_new.keys())[0]
output_pdf = '/content/highlighted_changes.pdf'
```
4. Perform the comparison and highlighting:

```
diff = compare_documents_semantically(old_pdf_path, new_pdf_path)
highlight_changes_semantically(diff, output_pdf, new_pdf_path)

print(f"Comparison and highlighting completed. Output saved to: {output_pdf}")

files.download(output_pdf)

```
### Contributing
We welcome contributions to this project! If you have any suggestions, bug reports, or feature requests, please feel free to open an issue or submit a pull request on the GitHub repository.
