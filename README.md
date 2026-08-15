# Bookstore-Management-System
# CBSE class 12 project, first attempt at a reudimentary backend management code
#----------------------------------------------------------
#BOOK STORE MANAGEMENT SYSTEM
#----------------------------------------------------------

import pickle

# -----------------------------
# FILE HANDLING FUNCTIONS
# -----------------------------

def load(filename):
    try:
        f = open(filename, "rb")
        data = pickle.load(f)
        f.close()
        return data
    except:
        return []


def save(filename, data):
    f = open(filename, "wb")
    pickle.dump(data, f)
    f.close()

# -----------------------------
# CONFIRMATION AND VALIDATION
# -----------------------------

def book_validate(bid):
    valbook = load(BOOK_FILE)
    for b in valbook:
        if bid == b["id"]:
            print("❌BOOK ID ALREADY EXISTS")
            return False
    return True

def cust_validate(cid):
    valcust = load(CUSTOMER_FILE)
    for c in valcust:
        if cid == c["id"]:
            print("❌CUSTOMER ALREADY EXISTS...")
            return False
    return True
        

# -----------------------------
# ADMIN SETUP & LOGIN
# -----------------------------

ADMIN_FILE = "admin.bin"

def setup_admin():
    admins = load(ADMIN_FILE)
    if admins != []:
        return                                                 # admin already exists

    print("\n--- Admin Setup (First Time Only) ---")
    user = input("Create Username: ")
    pwd = input("Create Password: ")

    admin = {"username": user, "password": pwd}
    save(ADMIN_FILE, [admin])

    print("✔Admin Created!")


def admin_login():
    admins = load(ADMIN_FILE)

    print("\n=== ADMIN LOGIN ===")
    u = input("Username: ")
    p = input("Password: ")

    if admins[0]["username"] == u and admins[0]["password"] == p:
        print("✔Login Successful!")
        return True
    else:
        print("❌Invalid Credentials.")
        return False


# -----------------------------
# BOOK FUNCTIONS
# -----------------------------

BOOK_FILE = "books.bin"

def add_book():
    books = load(BOOK_FILE)
    print("--- ADD BOOK ---")
    bid = int(input("Book ID: "))
    title = input("Title: ")
    author = input("Author: ")
    price = float(input("Price: "))
    stock = int(input("Stock: "))

    val = book_validate(bid)
    
    if val == True:
        book = {"id": bid, "title": title, "author": author, "price": price, "stock": stock}
        books.append(book)
        save(BOOK_FILE, books)

        print("✔Book Added!")


def view_books():
    books = load(BOOK_FILE)
    print("--- BOOK LIST ---")

    if books == []:
        print("❌No books available.")
        return

    for b in books:
        print(b)
    print()


def search_book():
    books = load(BOOK_FILE)
    print("--- SEARCH BOOK ---")
    bid = int(input("Enter Book ID: "))

    for b in books:
        if b["id"] == bid:
            print("✔Book Found:", b, "\n")
            return

    print("❌Book Not Found.")


def update_book():
    books = load(BOOK_FILE)
    bid = int(input("Enter Book ID to update: "))

    for b in books:
        if b["id"] == bid:
            print("Leave blank to keep old value.")
            t = input("New Title (" + b["title"] + "): ")
            a = input("New Author (" + b["author"] + "): ")
            p = input("New Price (" + str(b["price"]) + "): ")
            s = input("New Stock (" + str(b["stock"]) + "): ")

            if t != "":
                b["title"] = t
            if a != "":
                b["author"] = a
            if p != "":
                b["price"] = float(p)
            if s != "":
                b["stock"] = int(s)

            save(BOOK_FILE, books)
            print("✔Book Updated!")
            return

    print("❌Book Not Found.")


def delete_book():
    books = load(BOOK_FILE)
    bid = int(input("Enter Book ID to delete: "))

    new_list = [b for b in books if b["id"] != bid]

    if len(new_list) == len(books):
        print("❌Book Not Found.")
    else:
        save(BOOK_FILE, new_list)
        print("✔Book Deleted!")


# -----------------------------
# SALES FUNCTIONS
# -----------------------------

SALES_FILE = "sales.bin"

def make_sale():
    sales = load(SALES_FILE)
    books = load(BOOK_FILE)
    customers = load(CUSTOMER_FILE)                      # Refer under Customer Management Functions

    cid = int(input("Enter Customer ID: "))
    name = input("Enter Name: ")
    phone = input("Enter Phone: ")

    val = cust_validate(cid)
    
    if val == True:
        c = {"id": cid, "name": name, "phone": phone}
        customers.append(c)

        save(CUSTOMER_FILE, customers)
        print("✔ Customer added!")

    bid = int(input("Enter Book ID to Sell: "))

    for b in books:
        if b["id"] == bid:
            qty = int(input("Quantity: "))

            if qty > b["stock"]:
                print("❌Not enough stock!")
                return

            total = qty * b["price"]
            b["stock"] -= qty

            save(BOOK_FILE, books)

            sale = {"book_id": bid, "title": b["title"], "qty": qty, "total": total}
            sales.append(sale)
            save(SALES_FILE, sales)

            print("✔Sale Recorded! Total =", total, "\n")
            return

    print("❌Book Not Found.")


def view_sales():
    sales = load(SALES_FILE)
    print("--- SALES RECORDS ---")

    if sales == []:
        print("❌No sales yet.")
        return

    for s in sales:
        print(s)
    print()

# ------------------------------------------------------------
#   CUSTOMER MANAGEMENT FUNCTIONS
# ------------------------------------------------------------

CUSTOMER_FILE = "customers.bin"

def view_customers():
    customers = load(CUSTOMER_FILE)

    if customers == []:
        print("❌No customers found.")
        return

    print("--- CUSTOMER LIST ---")
    for c in customers:
        print(c)
    print()


def search_customer():
    customers = load(CUSTOMER_FILE)

    cid = int(input("Enter Customer ID: "))

    for c in customers:
        if c["id"] == cid:
            print("✔Found:", c)
            return

    print("❌Customer not found.")
    

# -----------------------------
# MAIN MENU
# -----------------------------

def main():
    setup_admin()

    if not admin_login():
        return

    while True:
        print("===== BOOKSTORE MENU =====")
        print("1.  Add Book")
        print("2.  View Books")
        print("3.  Search Book")
        print("4.  Update Book")
        print("5.  Delete Book")
        print("6.  Make Sale")                             # customer details added under sales
        print("7.  View Sales")
        print("8.  View Customers")
        print("9.  Search Customer")
        print("10. Exit")

        ch = input("Enter choice: ")

        if ch == "1": add_book()
        elif ch == "2":
            view_books()
        elif ch == "3":
            search_book()
        elif ch == "4":
            update_book()
        elif ch == "5":
            delete_book()
        elif ch == "6":
            make_sale()
        elif ch == "7":
            view_sales()
        elif ch == "8":
            view_customers()
        elif ch == "9":
            search_customer()
        elif ch == "10":
            print("✔Exiting...")
            break
        else:
            print("❌Invalid Choice.")


main()
