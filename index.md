---
title: [INDEX]
layout: home
---
- [reveng](./reveng.html)
- [vim](./vim.html)
- [sdr](./sdr.html)

# oscp prep and notes
## find
### find files by size in currecnt directory
- ```find . -type f -size <bytes>c```
- ```find . -type f | xargs grep "<pattern>"```
- ```find / -user <user|userid>```
- ```find / -group <group|groupid>```

## xxd
### make a hexdump or do the reverse
- ```xxd <infile> <outfile>```
- ```xxd -r <infile> <outfile>```
- ```cat <hexfile> | xxd -r```


## oscp - bof practice
- [reddit - Buffer overflow practice for OSCP?](https://www.reddit.com/r/netsecstudents/comments/9ld0ps/buffer_overflow_practice_for_oscp/)
- [OSCP Course & Exam Preparation](https://411hall.github.io/OSCP-Preparation/)
