# Python Beginner Challenge 2: Address Book with CSV File Storage (Easy)

## Challenge Theme

**Extend your Address Book to save and load contacts using a CSV file.**

### Prerequisites

Complete **Challenge 1: Address Book (OOP Basics)** before attempting this challenge.

---

### Learning Objectives

By completing this challenge, you will learn:

* How to import and use Python's built-in `csv` module
* How to write data to a CSV file
* How to read data from a CSV file
* How to automatically save and load data so the program remembers contacts between runs
* How to make an application **stateful** (data persists after the program closes)

---

# Challenge Statement (Step-by-Step)

## Step 1: Import the csv module

At the very top of your file, before any class definitions, import Python's built-in csv module.

```
import csv
```

No installation is needed. It is already part of Python.

---

## Step 2: Choose a filename constant

Below your import, create a constant to hold the name of the CSV file that will store all contacts.

```
FILENAME = "contacts.csv"
```

Using a constant means you only need to change the filename in one place if you ever rename the file.

---

## Step 3: Keep your Contact class exactly the same

Do **not** change the `Contact` class from Challenge 1.

It still has:

* `name`
* `phone`
* `email`
* `display_contact()`

---

## Step 4: Add a save method to AddressBook

Inside the `AddressBook` class, create a new method called

```
save_contacts()
```

This method should:

* Open the CSV file in **write** mode (`"w"`)
* Use `csv.writer` to create a writer object
* Write a **header row** first: `["Name", "Phone", "Email"]`
* Loop through every contact in the list and write one row per contact

Call this method **automatically** at the end of `add_contact()`.

---

## Step 5: Add a load method to AddressBook

Inside the `AddressBook` class, create a new method called

```
load_contacts()
```

This method should:

* Try to open the CSV file in **read** mode (`"r"`)
* Skip the header row
* Read each remaining row and create a `Contact` object from it
* Append each `Contact` object to `self.contacts`
* If the file does not exist yet, do nothing (use a `try / except` block)

---

## Step 6: Call load_contacts inside the constructor

Inside the `AddressBook` `__init__` method, after creating the empty list, call

```
self.load_contacts()
```

This makes the program automatically restore all previously saved contacts every time it starts.

---

## Step 7: Call save_contacts after every change

Make sure `save_contacts()` is called at the end of:

* `add_contact()` — after appending the new contact
* Any bonus methods you add later (delete, update, etc.)

This keeps the CSV file always up to date.

---

## Step 8: Keep the Main Menu exactly the same

The `while` loop and the `if-elif-else` menu from Challenge 1 do **not** need to change.

The save and load operations happen automatically behind the scenes.

---

## Step 9: Test persistence

Run the program.

Add two or three contacts.

Exit with option 4.

Run the program again.

Choose option 2 (View Contacts).

You should see all the contacts you entered in the previous run — they were loaded from the CSV file automatically.

---

# Concepts Used

✅ Classes

✅ Objects

✅ Constructor

✅ Instance Variables

✅ Strings

✅ Lists

✅ Methods

✅ for loop

✅ while loop

✅ if-elif-else

✅ User Input

✅ **import statement**

✅ **csv module**

✅ **File reading and writing**

✅ **try / except**

✅ **Constants**

✅ **Stateful application (data persistence)**

---

# Complete Solution (Each Code Section References the Challenge Steps)

```python
# ==========================================================
# Step 1:
# Import the built-in csv module.
# ==========================================================

import csv

# ==========================================================
# Step 2:
# Constant for the CSV filename.
# ==========================================================

FILENAME = "contacts.csv"


# ==========================================================
# Step 3:
# Contact class — unchanged from Challenge 1.
# ==========================================================

class Contact:

    # Step 3:
    # Constructor initializes the contact information.
    def __init__(self, name, phone, email):
        self.name = name          # String
        self.phone = phone        # String
        self.email = email        # String

    def display_contact(self):
        print("------------------------")
        print("Name :", self.name)
        print("Phone:", self.phone)
        print("Email:", self.email)
        print("------------------------")


# ==========================================================
# AddressBook class — extended with save and load methods.
# ==========================================================

class AddressBook:

    def __init__(self):

        # Step 6:
        # Start with an empty list.
        self.contacts = []

        # Step 6:
        # Automatically load saved contacts when the program starts.
        self.load_contacts()

    # ==========================================================
    # Step 4:
    # Save all contacts to the CSV file.
    # ==========================================================
    def save_contacts(self):

        # Step 4:
        # Open the file in write mode.
        # newline="" prevents extra blank lines on Windows.
        with open(FILENAME, "w", newline="") as file:

            # Step 4:
            # Create a csv writer object.
            writer = csv.writer(file)

            # Step 4:
            # Write the header row first.
            writer.writerow(["Name", "Phone", "Email"])

            # Step 4:
            # Write one row for every contact.
            for contact in self.contacts:
                writer.writerow([contact.name, contact.phone, contact.email])

    # ==========================================================
    # Step 5:
    # Load contacts from the CSV file.
    # ==========================================================
    def load_contacts(self):

        # Step 5:
        # Use try/except so the program does not crash
        # if the file does not exist yet (first run).
        try:
            with open(FILENAME, "r") as file:

                # Step 5:
                # Create a csv reader object.
                reader = csv.reader(file)

                # Step 5:
                # Skip the header row.
                next(reader)

                # Step 5:
                # Read each remaining row and create Contact objects.
                for row in reader:

                    # Step 5:
                    # Each row is a list: [name, phone, email]
                    name = row[0]
                    phone = row[1]
                    email = row[2]

                    # Step 5:
                    # Create a Contact object and add it to the list.
                    contact = Contact(name, phone, email)
                    self.contacts.append(contact)

        except FileNotFoundError:
            # Step 5:
            # File does not exist yet — this is fine on the first run.
            pass

    # ==========================================================
    # add_contact — same as Challenge 1, with save added at the end.
    # ==========================================================
    def add_contact(self):

        name = input("Enter Name : ")
        phone = input("Enter Phone: ")
        email = input("Enter Email: ")

        contact = Contact(name, phone, email)
        self.contacts.append(contact)

        # Step 7:
        # Save immediately after adding so no data is lost.
        self.save_contacts()

        print("Contact Added Successfully!")

    # ==========================================================
    # view_contacts — unchanged from Challenge 1.
    # ==========================================================
    def view_contacts(self):

        if len(self.contacts) == 0:
            print("No contacts found.")
            return

        for contact in self.contacts:
            contact.display_contact()

    # ==========================================================
    # search_contact — unchanged from Challenge 1.
    # ==========================================================
    def search_contact(self):

        search_name = input("Enter name to search: ")
        found = False

        for contact in self.contacts:
            if contact.name.lower() == search_name.lower():
                contact.display_contact()
                found = True
                break

        if not found:
            print("Contact not found.")


# ==========================================================
# Step 8:
# Main menu — unchanged from Challenge 1.
# The save/load now happens automatically inside the class.
# ==========================================================

address_book = AddressBook()

while True:

    print("\n===== ADDRESS BOOK =====")
    print("1. Add Contact")
    print("2. View Contacts")
    print("3. Search Contact")
    print("4. Exit")

    choice = input("Enter your choice: ")

    if choice == "1":
        address_book.add_contact()

    elif choice == "2":
        address_book.view_contacts()

    elif choice == "3":
        address_book.search_contact()

    elif choice == "4":
        print("Thank you for using Address Book!")
        break

    else:
        print("Invalid choice. Please try again.")
```

---

## What the CSV file looks like

After adding two contacts the file `contacts.csv` will contain:

```
Name,Phone,Email
Alice,9876543210,alice@gmail.com
Bob,9123456789,bob@gmail.com
```

You can open this file in any spreadsheet program (Excel, Google Sheets) or a plain text editor.

---

## Bonus Challenges (Optional)

Once students complete Challenge 2, encourage them to extend it further:

1. Add a **Delete Contact** option that removes a contact by name and saves the updated list.
2. Add an **Update Contact** option that lets the user change a phone number or email and saves automatically.
3. Show a **Contact Count** by printing `len(self.contacts)` from a new menu option.
4. **Sort** the contacts alphabetically by name before displaying them (`sorted()`).
5. Add **input validation** so the phone field accepts only digits and the email must contain `@`.
6. Prevent **duplicate contacts** by checking name and phone before adding.
7. Let the user choose a **custom filename** at startup and store it in the constant.

These extensions reinforce file I/O and stateful application design while building on the OOP foundation from Challenge 1.
