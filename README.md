# Full RAG UI: PDF Assistant

A chat app that reads scanned and handwritten PDFs and answers questions about them, with the page number and source text shown under every answer.

I built this as my final project for the Outamation AI-Powered Document Insights and Data Extraction Externship (through Extern), finished in December 2025.

## What it does

1. You upload one or more PDFs.
2. The app reads every page with OCR, even if the page is a scan or has handwriting.
3. You ask a question in the chat.
4. It finds the parts of the document that match your question and writes an answer using only that text.
5. Under the answer it shows the top sources: page number, similarity score, and a snippet.

## How it works

| Step | What happens | Tools |
| --- | --- | --- |
| OCR | Each page is rendered as an image at 3x zoom, cleaned up with contrast enhancement (CLAHE) and thresholding, then read | PyMuPDF, OpenCV, EasyOCR |
| Chunking | Text is split into chunks of 512 with an overlap of 50. Each chunk is tagged with its file name, page number and document type | LlamaIndex |
| Indexing | Chunks are turned into embeddings and saved to disk so the index can be reloaded | BGE-small (Hugging Face), LlamaIndex |
| Answering | A local model gets the top 5 matching chunks and answers only from them, at a low temperature | Gemma 2 (2B) through Ollama |
| Interface | File upload on the left, chat on the right, sources under each answer | Gradio |
| Benchmark | Times OCR, indexing and the model separately and charts the results | pandas, Matplotlib |

## Why I made these choices

- **CLAHE before OCR.** Handwritten dates were getting misread. Boosting the contrast made the ink easier to separate from the paper.
- **Page numbers on every chunk.** This is what lets the app say where an answer came from instead of just giving an answer.
- **Local model.** Gemma 2 runs inside the notebook through Ollama, so documents are not sent to an outside API.
- **Low temperature (0.1).** The goal is to pull facts out of a document, not to be creative.

## How to run it

1. Open `Full_RAG_UI.ipynb` in Google Colab.
2. Switch the runtime to a GPU (Runtime > Change runtime type).
3. Run the cells in order. The first cell installs everything and takes about two minutes.
4. Run the last UI cell and open the Gradio link it prints.
5. Upload a PDF, click **Analyze Documents**, then ask a question.

To use the test and benchmark cells, upload your own PDF to Colab and change `TEST_FILE` and `TEST_FILE_PATH` to its path.

## Notes

- The mortgage document I tested with is a sample file with no real personal information. It is not included in this repo.
- The score shown with each source is a similarity score. It tells you how closely the retrieved text matches the question, not whether the answer is correct.

## About me

Mac Mckay, data science student at Santa Monica College. More projects on my [GitHub profile](https://github.com/raulmckay-blip).
