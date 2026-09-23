# piiscrub

piiscrub finds personally identifiable information (PII) in PDF, Word (.docx)
and Excel (.xlsx) documents and replaces it with realistic substitute data, so
the documents stay readable and keep their layout while no longer identifying
anyone. It runs entirely on your own machine and makes no network calls.

This repository publishes piiscrub releases. Each release attaches a source
archive with installation instructions in its own README.

**[Download the latest release](https://github.com/covecta/piiscrub-releases/releases/latest)**

## What it does

- Detects names, addresses, towns, postcodes, email addresses, phone numbers,
  dates of birth, National Insurance and NHS numbers, bank sort codes, account
  numbers, IBANs, card numbers, employee numbers and customer references. UK
  formats are covered out of the box; detection is configurable through a YAML
  file.
- Replaces each value with a realistic substitute of the same shape: a fake
  name keeps any title and initials, a phone number keeps its width and
  prefix, a date of birth keeps its format. The same original always gets the
  same substitute throughout a run.
- Covers text outside the visible page as well: headers and footers,
  comments and their authors, tracked changes, hyperlinks, document
  properties and metadata, sheet names, PDF links, annotations and form fields,
  and file names.
- Handles scanned PDFs with local OCR (Tesseract). The original text is
  removed from the page image itself, not just covered by a box, so it cannot
  be recovered by extracting the image.
- Re-checks every output and reports any original value it can still find.

Detection uses [Presidio](https://github.com/data-privacy-stack/presidio) with
the spaCy `en_core_web_lg` language model for names and places, plus pattern
rules for structured identifiers.

## Requirements

- Python 3.12
- [Tesseract](https://github.com/tesseract-ocr/tesseract), only for scanned PDFs
- macOS, Linux or Windows

## Quick start

Download `piiscrub-<version>.zip` and `SHA256SUMS` from the
[releases page](https://github.com/covecta/piiscrub-releases/releases), then:

```bash
shasum -a 256 -c SHA256SUMS --ignore-missing
unzip piiscrub-<version>.zip && cd piiscrub-<version>
python3.12 -m venv .venv && source .venv/bin/activate
pip install -r requirements.lock
cp config.example.yaml config.yaml
python -m src.cli run --in ./input_docs --out ./clean_docs --config config.yaml
```

The README inside the archive covers Windows, configuration, every
substitution strategy, the run manifest and exit codes in full.

## Accuracy

Each release is measured against a synthetic test set of letters, forms, bank
statements and payslips in Word, Excel, native PDF and scanned PDF form, where
every planted item of PII is checked individually. Release 0.2.0 removed
1,622 of 1,623 planted items (99.9%) and left all 2,212 ordinary text items
that should be kept unchanged.

These figures describe synthetic documents. Real documents vary, and detection
will miss some PII and flag some text that is not PII. Review every output
before sharing it.

## Privacy

- No network access at run time. The spaCy model is installed once with the
  dependencies; piiscrub refuses to start without it rather than downloading
  it.
- Nothing is stored between runs. The mapping from originals to substitutes
  exists only in memory while a batch is processed.
- The run manifest records original file names, so it is written beside the
  output folder rather than inside it.

## Reporting problems

Please open an issue in this repository. Do not attach real documents or any
personal data; describe the layout or share a synthetic example instead.

## Licence

[MIT](LICENSE). Copyright (c) 2026 Mark Hammond.
