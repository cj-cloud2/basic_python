# Python Beginner Challenge: Address Book (Easy)

## Challenge Theme

**Build a simple Address Book using Object-Oriented Programming (OOP).**

### Learning Objectives

By completing this challenge, you will learn:

* How to create a class
* How to create objects
* How to use instance variables
* How to use basic data types inside a class
* How to write methods
* How to use `if`, `while`, and `for` loops
* How to use lists to store objects
* How to take user input and display information

---

# Challenge Statement (Step-by-Step)

## Step 1: Create a Contact class

Create a class named **Contact**.

Inside the class create the following attributes:

* name (string)
* phone (string)
* email (string)

These should be initialized using a constructor (`__init__`).

---

## Step 2: Create a method to display contact details

Inside the Contact class create a method called

`display_contact()`

It should print all details of the contact in a readable format.

Example

```
Name : Alice
Phone: 9876543210
Email: alice@gmail.com
```

---

## Step 3: Create an AddressBook class

Create another class named

`AddressBook`

Inside this class create a list called

```
contacts
```

Initially the list should be empty.

---

## Step 4: Add a contact

Create a method

```
add_contact()
```

Inside this method

* Ask the user for name
* Ask the user for phone number
* Ask the user for email

Create a Contact object using these values.

Store the Contact object inside the contacts list.

Print

```
Contact Added Successfully!
```

---

## Step 5: View all contacts

Create a method

```
view_contacts()
```

Use a **for loop** to display every contact.

If the address book is empty, print

```
No contacts found.
```

Use an **if statement** to check this condition.

---

## Step 6: Search for a contact

Create a method

```
search_contact()
```

Ask the user for a name.

Use a **for loop** to search through the contacts list.

If a matching name is found

* Display the contact.

Otherwise print

```
Contact not found.
```

---

## Step 7: Create the Main Menu

Create an AddressBook object.

Use a **while loop** to repeatedly show the following menu.

```
1. Add Contact
2. View Contacts
3. Search Contact
4. Exit
```

---

## Step 8: Use flow control

Use **if-elif-else** to perform the selected option.

Example

```
if choice == 1
    add contact

elif choice == 2
    view contacts

...
```

---

## Step 9: Exit the program

If the user enters

```
4
```

Print

```
Thank you for using Address Book!
```

Then stop the loop.

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

---

# Complete Solution (Each Code Section References the Challenge Steps)

```python
# ==========================================================
# Step 1:
# Create the Contact class with name, phone, and email.
# ==========================================================

class Contact:

    # Step 1:
    # Constructor initializes the contact information.
    def __init__(self, name, phone, email):
        self.name = name          # String
        self.phone = phone        # String
        self.email = email        # String

    # ==========================================================
    # Step 2:
    # Display the contact details.
    # ==========================================================
    def display_contact(self):
        print("------------------------")
        print("Name :", self.name)
        print("Phone:", self.phone)
        print("Email:", self.email)
        print("------------------------")


# ==========================================================
# Step 3:
# Create the AddressBook class.
# ==========================================================

class AddressBook:

    # Step 3:
    # Constructor creates an empty list to store Contact objects.
    def __init__(self):
        self.contacts = []

    # ==========================================================
    # Step 4:
    # Add a new contact.
    # ==========================================================
    def add_contact(self):

        # Step 4:
        # Take user input.
        name = input("Enter Name : ")
        phone = input("Enter Phone: ")
        email = input("Enter Email: ")

        # Step 4:
        # Create a Contact object.
        contact = Contact(name, phone, email)

        # Step 4:
        # Store the Contact object in the list.
        self.contacts.append(contact)

        # Step 4:
        # Confirmation message.
        print("Contact Added Successfully!")

    # ==========================================================
    # Step 5:
    # View all contacts.
    # ==========================================================
    def view_contacts(self):

        # Step 5:
        # Check whether the address book is empty.
        if len(self.contacts) == 0:
            print("No contacts found.")
            return

        # Step 5:
        # Use a for loop to display every contact.
        for contact in self.contacts:
            contact.display_contact()

    # ==========================================================
    # Step 6:
    # Search for a contact.
    # ==========================================================
    def search_contact(self):

        # Step 6:
        # Ask the user for the name to search.
        search_name = input("Enter name to search: ")

        # Step 6:
        # Variable to remember whether a match was found.
        found = False

        # Step 6:
        # Search using a for loop.
        for contact in self.contacts:

            # Step 6:
            # Compare names.
            if contact.name.lower() == search_name.lower():
                contact.display_contact()
                found = True
                break

        # Step 6:
        # If no contact matched, display a message.
        if not found:
            print("Contact not found.")


# ==========================================================
# Step 7:
# Create an AddressBook object.
# ==========================================================

address_book = AddressBook()

# ==========================================================
# Step 7:
# Use a while loop to repeatedly display the menu.
# ==========================================================

while True:

    print("\n===== ADDRESS BOOK =====")
    print("1. Add Contact")
    print("2. View Contacts")
    print("3. Search Contact")
    print("4. Exit")

    # Step 7:
    # Read the user's menu choice.
    choice = input("Enter your choice: ")

    # ==========================================================
    # Step 8:
    # Use if-elif-else to perform the selected action.
    # ==========================================================

    if choice == "1":
        address_book.add_contact()

    elif choice == "2":
        address_book.view_contacts()

    elif choice == "3":
        address_book.search_contact()

    # ==========================================================
    # Step 9:
    # Exit the program.
    # ==========================================================
    elif choice == "4":
        print("Thank you for using Address Book!")
        break

    else:
        print("Invalid choice. Please try again.")
```

## Bonus Challenges (Optional)

Once students complete the basic challenge, encourage them to extend it by adding:

1. Delete a contact by name.
2. Update a contact's phone number or email.
3. Count the total number of contacts.
4. Sort contacts alphabetically before displaying them.
5. Save contacts to a text file and load them when the program starts.
6. Prevent duplicate contacts with the same name and phone number.
7. Add input validation to ensure the phone number contains only digits and the email contains an `@` symbol.

These extensions reinforce the same foundational concepts while gradually introducing more problem-solving and object-oriented design.
