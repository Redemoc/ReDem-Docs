# ReDem import workbook helper

This repository includes a small utility for preparing Excel imports where **row 1** contains variable identifiers and **row 2** contains full question texts (see the file import documentation).

## Usage

Install dependency:

```bash
pip install openpyxl
```

### Convert a CSV that already has the two header rows

```bash
python3 tools/transform_redem_import_workbook.py --from-csv path/to/data.csv path/to/output.xlsx
```

### Normalize a messy Excel header row

```bash
python3 tools/transform_redem_import_workbook.py path/to/input.xlsx path/to/output.xlsx
```

The Excel mode supports common cases such as `Q1A1_1: Question text…` in the first row, bare IDs in the first row with questions in the second row, and questions without IDs (synthetic `AUTO_Q###` identifiers are added).
