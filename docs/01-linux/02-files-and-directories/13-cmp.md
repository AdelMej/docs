# cmp Command Documentation

## Name and Synopsis

* **Description:** `cmp` is used to compare two files byte by byte.

* **Basic Usage:**

```bash
cmp [OPTION]... FILE1 [FILE2 [SKIP1 [SKIP2]]]
```

---

## Description

The `cmp` command compares the contents of two files byte by byte. It can be configured to skip a specific number of bytes at the beginning of each file, which defaults to zero. If a file argument is replaced with `-` or is missing, the command reads from standard input. The command also supports multiplicative suffixes for skipping values, such as kB (1000), K (1024), MB (1,000,000), M (1,048,576), GB (1,000,000,000), and G (1,073,741,824).

---

## Options Reference

| Option | Long Form | Description |
| :--- | :--- | :--- |
| -b | --print-bytes | Print the differing bytes. |
| -i | --ignore-initial=SKIP | Skip the first SKIP bytes of both inputs. |
| -i | --ignore-initial=SKIP1:SKIP2 | Skip first SKIP1 bytes of FILE1 and first SKIP2 bytes of FILE2. |
| -l | --verbose | Output byte numbers and differing byte values. |
| -n | --bytes=LIMIT | Compare at most LIMIT bytes. |
| -s | --quiet, --silent | Suppress all normal output. |
| --help | N/A | Display help information and exit. |
| -v | --version | Output version information and exit. |

---

## Examples

* **Compare two files:**

```bash
cmp file1 file2
```

* **Verbose comparison showing byte numbers:**

```bash
cmp -l file1 file2
```

* **Compare files while skipping the first 10 bytes:**

```bash
cmp -i 10 file1 file2
```

* **Compare only the first 1 megabyte of two files:**

```bash
cmp -n 1M file1 file2
```

---

## Exit Values

* 0: The inputs are the same.
* 1: The inputs are different.
* 2: An error occurred during comparison (trouble).

---

## Caveats / Notes

* **Argument Consistency:** Mandatory arguments provided for long options are also mandatory for short options.
* **Standard Input:** Using `-` or omitting a filename will cause the command to read from standard input.
