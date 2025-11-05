# NDJSON Splitter

A lightweight, memory-efficient Python script to split large newline-delimited JSON (NDJSON) files into smaller, manageable parts. This is useful for processing datasets that are too large to fit into memory.

## Quick links
- Original dataset (example used by the script): https://business.yelp.com/data/resources/open-dataset/

## Table of contents
- What this does
- Requirements
- How to use
- How it works
- Limitations & edge cases
- Suggested improvements
- Files in this repo
- License & contact

## What this does

`split_files.py` reads a newline-delimited JSON file (each line is a standalone JSON object) and splits it into `N` smaller files. It's useful when you need to process or load very large JSON files but want smaller, more manageable chunks.

## Requirements

- Python 3.7+ (works with modern Python 3 versions)
- Enough disk space for the output files

No external packages are required — the script uses only the Python standard library.

## How to use

1. Place your NDJSON file (one JSON object per line) in the same folder as `split_files.py` or update the path in the script.
2. Edit the variables at the top of `split_files.py` to match your file and desired output:

```python
input_file = "yelp_academic_dataset_review.json"  # Path to your NDJSON file
output_prefix = "split_file_"  # Output file prefix
num_files = 10  # Number of parts to create
```

3. Run the script:

```
python split_files.py
```

After running, you'll get `num_files` files named `{output_prefix}1.json`, `{output_prefix}2.json`, ..., each containing an approximately equal number of lines.

## How it works (implementation details)

1. The script first counts the total number of lines in the input file by scanning it once.
2. It computes `lines_per_file = total_lines // num_files` using integer division.
3. It re-opens the input file and writes `lines_per_file` lines to each output file in sequence.

This approach is memory-efficient because it never loads the whole file into memory — it streams the file line-by-line. However it does read the input twice (once for counting, once for splitting).

## Limitations & edge cases

- Input format: The script expects NDJSON (one valid JSON object per line). It will not work correctly for a single large JSON array or pretty-printed JSON containing newlines inside objects.
- Remainder lines: Because the script uses integer division, any remainder (when `total_lines % num_files != 0`) will currently be ignored — those leftover lines won't be written. To preserve all lines, modify the script to distribute the remainder (for example, write extra lines to the last file).
- Very large files: The script streams data, so memory usage stays low, but splitting requires reading the file twice. For extremely large files on slow disks this will take time.
- Encoding: The script opens files with UTF-8 encoding. Adjust if your data uses a different encoding.

## Suggested improvements

- Add command-line arguments (argparse) to pass `input_file`, `output_prefix`, and `num_files` instead of editing the script.
- Preserve remainder lines by distributing them (e.g., the last file receives the extra lines).
- Add logging and a progress indicator for large files.
- Support splitting by approximate byte size (instead of line count) for non-NDJSON inputs.
- Optionally compress outputs (gzip) to save disk space.

## Files in this repo

- `split_files.py` — The splitting script. Edit the variables at the top or convert to a CLI.
- `UDF and tables.sql` — (SQL file included in repo). Review as needed for DB-related tasks.

## Example: small CLI snippet

If you'd like to quickly convert `split_files.py` to accept command-line arguments, replace the top variables with `argparse` and call the same logic. This is a low-risk enhancement and recommended if you run the script often with different inputs.

## License

This repository is provided as-is. Add a license file if you intend to publish or share the project (MIT, Apache 2.0, etc.).

## Contact

If you want help improving the script (adding CLI support, handling remainders, or adding tests), open an issue or reach out directly.

---

Generated README for the small utility that splits large NDJSON files into smaller parts.