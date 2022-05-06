# PDF operations

## 1 pdftk

### 1.1 Extract pages from a pdf file
```bash
pdftk input.pdf cat start-end output output_start-end.pdf
```

e.g.:
Extract the second page of main.pdf
```bash
pdftk main.pdf cat 2 output main-2.pdf
```

### 1.2 Merge several pdf files
```bash
pdftk 1.pdf 2.pdf 3.pdf cat output main.pdf
```
