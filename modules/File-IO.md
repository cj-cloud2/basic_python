# Module 1: Introduction to File Handling

## Learning Objectives

Students should understand:

* Why files are needed
* Difference between memory and permanent storage
* What happens when a program exits
* Types of files (Text vs Binary)


```
Without a file

Program Starts
      │
      ▼
Store data in variables
      │
      ▼
Program Ends
      │
      ▼
Data Lost
```

```
With a file

Program Starts
      │
      ▼
Read file
      │
      ▼
Work with data
      │
      ▼
Save file
      │
      ▼
Program Ends
      │
      ▼
Data Preserved
```

---

# Module 2: Opening a File

Teach the syntax

```python
file = open("students.txt", "r")
```

Explain

* filename
* mode

---

# Module 3: File Modes

Students should know these four modes.

### Read

```python
open("students.txt", "r")
```

Reads existing file.

---

### Write

```python
open("students.txt", "w")
```

Creates file if needed.

Deletes previous contents.

---

### Append

```python
open("students.txt", "a")
```

Adds new data to the end.

---

### Read and Write (optional)

```python
open("students.txt", "r+")
```

Introduce later if needed.

---

# Module 4: Closing Files

Traditional way

```python
file.close()
```

Explain why files should be closed.

---

# Module 5: Using with

Introduce

```python
with open("students.txt", "r") as file:
```

Explain

Python automatically closes the file.

Students usually find this easier.

---

# Module 6: Writing Data

Simple text

```python
with open("notes.txt", "w") as file:
    file.write("Hello")
```

---

Multiple lines

```python
file.write("Alice\n")
file.write("Bob\n")
```

---

# Module 7: Reading Data

Read everything

```python
content = file.read()
```

---

Read one line

```python
line = file.readline()
```

---

Read all lines

```python
lines = file.readlines()
```

---

# Module 8: Reading Using a Loop

```python
with open("notes.txt") as file:

    for line in file:
        print(line)
```

Good introduction to iteration.

---

# Module 9: CSV Files

Why CSV?

Instead of

```
Alice
98765
alice@gmail.com
```

Store

```
Alice,98765,alice@gmail.com
```

Explain rows and columns.

---

# Module 10: Importing csv

```python
import csv
```

Explain

csv is a built-in module.

---

# Module 11: Reading CSV

```python
with open("contacts.csv", "r") as file:

    reader = csv.reader(file)

    for row in reader:
        print(row)
```

Explain

```
row
```

is a list.

Example

```
['Alice', '9876543210', 'alice@gmail.com']
```

---

# Module 12: Writing CSV

```python
with open("contacts.csv", "w", newline="") as file:

    writer = csv.writer(file)

    writer.writerow(["Alice", "9876543210", "alice@gmail.com"])
```

Explain

```
writerow()
```

writes one row.

---

# Module 13: Multiple Rows

```python
for contact in contacts:

    writer.writerow([
        contact.name,
        contact.phone,
        contact.email
    ])
```

Excellent practice with objects.

---

# Module 14: Using Files with Classes

Students already know

```
Contact
```

Teach

```
CSV Row
        ↓

Contact Object
```

and

```
Contact Object
        ↓

CSV Row
```

This is an important programming concept.

---

# Module 15: Stateful Applications

Explain

Without files

```
Program
    ↓

Memory

    ↓

Exit

Data Lost
```

With files

```
Program
    ↓

Load File

    ↓

Memory

    ↓

Save File

    ↓

Exit
```

