# Verifying File Integrity with Hashing in Linux

> **Disclaimer:** This project was completed as part of the Google Cybersecurity Certificate (Coursera). The scenario and files come from the course. The commands, outputs, and explanations are my own documentation of the work I performed.

## Project description
I needed to investigate whether two files were identical or different. Using Linux commands, I displayed the contents of both files, generated a SHA-256 hash for each, saved the hashes to separate files, and compared them. Hashing is how security teams verify data integrity and detect tampering, such as a malicious program disguised as a legitimate one.

## Background: Hashing
A hash function is an algorithm that produces a fixed-length code, called a hash value or digest, that uniquely identifies the contents of a file. Hashes can't be decrypted back into the original data, and changing even one character of the input produces a completely different hash. That makes them useful for checking whether a file has been modified.

## Commands Used
| Command | Purpose |
|---|---|
| `ls` | List files in a directory |
| `cat` | Display the contents of a file |
| `sha256sum` | Generate a SHA-256 hash of a file |
| `>>` | Redirect output into a file, appending it if the file already exists |
| `cmp` | Compare two files byte by byte and report the first difference |

## Generate hashes for files
The home directory contains two files, `file1.txt` and `file2.txt`. I listed them and displayed the contents of each:

```bash
ls
cat file1.txt
cat file2.txt
```
Output:
```
file1.txt  file2.txt
```
```
X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*
```
```
X5O!P%@AP[4\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*
```

Both files appear identical. The text is the EICAR standard antivirus test string, a harmless file used to test whether antivirus software is working. Looking at the contents isn't enough to confirm the files are the same, so I generated a SHA-256 hash for each:

```bash
sha256sum file1.txt
sha256sum file2.txt
```
Output:
```
131f95c51cc819465fa1797f6ccacf9d494aaaff46fa3eac73ae63ffbdfd8267  file1.txt
2558ba9a4cad1e69804ce03aa2a029526179a91a5e38cb723320e83af9ca017b  file2.txt
```

The two hashes are completely different, so the files are **not** identical even though `cat` displays the same output.

## Compare hashes
To compare the hashes manually, I wrote each one to its own file using `>>`:

```bash
sha256sum file1.txt >> file1hash
sha256sum file2.txt >> file2hash
ls
```
Output:
```
file1.txt  file1hash  file2.txt  file2hash
```

Then I used `cmp` to compare the two hash files byte by byte:

```bash
cmp file1hash file2hash
```
Output:
```
file1hash file2hash differ: char 1, line 1
```

The files differ at the first character of the first line. This confirms the two hash values don't match, which means `file1.txt` and `file2.txt` are different files.

## Findings
| Check | Result |
|---|---|
| Contents of `file1.txt` and `file2.txt` with `cat` | Appear identical |
| SHA-256 hashes of the two files | Different |
| `cmp file1hash file2hash` | Differ at char 1, line 1 |
| Conclusion | The files are different |

Viewing the files can't prove they match, but comparing hashes can. In a real investigation, this is how an analyst would catch a modified file that looks the same as the original.

## Full Command Sequence
```bash
ls
cat file1.txt
cat file2.txt
sha256sum file1.txt
sha256sum file2.txt
sha256sum file1.txt >> file1hash
sha256sum file2.txt >> file2hash
ls
cmp file1hash file2hash
```

## Summary
I generated SHA-256 hashes for two files that looked identical, saved them to separate files, and compared them with `cmp`. The hashes were different, which showed the files had been altered in some way that wasn't visible in their displayed contents. Comparing hashes is a fast and reliable way to validate data integrity, and it's a core technique for detecting tampered or malicious files.

## Key Takeaways
- Looking at a file's contents isn't enough to prove two files are the same. A hash comparison is.
- A hash is a unique fingerprint of a file's contents. Any change to the file, even a single character, produces a completely different hash.
- Hash functions are one-way: you can't recover the original data from a hash, which is what separates hashing from encryption.
- A mismatch between a file's hash and a known-good hash is a sign the file was modified or replaced, which is how malware that mimics a legitimate program can be caught.
- `>>` appends to a file (and creates it if it doesn't exist), while `>` overwrites it, so `>>` is safer when you don't want to lose existing data.
- `cmp` is a quick way to compare two files byte by byte and find exactly where they first differ.

## Tools
- Linux Bash shell
- `sha256sum`, `cmp`, `cat`, `ls`
