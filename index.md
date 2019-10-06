---
layout: default
---
[reveng](./reveng.html) | [vim](./vim.html) | [sdr](./sdr.html)

# oscp prep and notes
## find
### find files by size in currecnt directory
- ```find . -type f -size <bytes>c```
- ```find . -type f | xargs grep "<pattern>"```

## xxd
### make a hexdump or do the reverse
- ```xxd <infile> <outfile>```
- ```xxd -r <infile> <outfile>```
- ```cat <hexfile> | xxd -r```
