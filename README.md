import sqlite3

# ==========================================
# 1. CONNECT TO DATABASE
# ==========================================

connection = sqlite3.connect("expenses.db")
cursor = connection.cursor()

# ==========================================
# 2. CREATE TABLE
# ==========================================

cursor.execute("""
CREATE TABLE IF NOT EXISTS transactions (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    date TEXT NOT NULL,
    amount REAL NOT NULL,
    category TEXT NOT NULL,
    description TEXT,
    upi_app TEXT NOT NULL
)
""")

connection.commit()

# ==========================================
# 3. INSERT TRANSACTION
# ==========================================

def add_transaction():
    print("\n----- Add Transaction -----")

    date = input("Enter date (DD-MM-YYYY): ")
    amount = input("Enter amount: ")
    category = input("Enter category: ")
    description = input("Enter description: ")
    upi_app = input("Enter UPI app: ")

    # Input validation
    if date == "" or category == "" or upi_app == "":
        print("Please fill all required fields.")
        return

    try:
        amount = float(amount)

        if amount <= 0:
            print("Amount must be greater than 0.")
            return

    except ValueError:
        print("Please enter a valid amount.")
        return

    # INSERT data into database
    cursor.execute("""
    INSERT INTO transactions
    (date, amount, category, description, upi_app)
    VALUES (?, ?, ?, ?, ?)
    """, (date, amount, category, description, upi_app))

    connection.commit()

    print("Transaction added successfully!")

# ==========================================
# 4. SELECT / DISPLAY TRANSACTIONS
# ==========================================

def view_transactions():
    print("\n----- All Transactions -----")

    cursor.execute("SELECT * FROM transactions")

    records = cursor.fetchall()

    if len(records) == 0:
        print("No transactions found.")
        return

    for record in records:
        print("--------------------------------")
        print("ID          :", record[0])
        print("Date        :", record[1])
        print("Amount      : ₹", record[2])
        print("Category    :", record[3])
        print("Description :", record[4])
        print("UPI App     :", record[5])

# ==========================================
# 5. DELETE TRANSACTION
# ==========================================

def delete_transaction():
    print("\n----- Delete Transaction -----")

    try:
        transaction_id = int(input("Enter transaction ID to delete: "))

        cursor.execute(
            "DELETE FROM transactions WHERE id = ?",
            (transaction_id,)
        )

        connection.commit()

        if cursor.rowcount > 0:
            print("Transaction deleted successfully!")
        else:
            print("Transaction ID not found.")

    except ValueError:
        print("Please enter a valid ID.")

# ==========================================
# 6. MAIN MENU
# ==========================================

while True:

    print("\n================================")
    print("      UPI EXPENSE TRACKER")
    print("================================")
    print("1. Add Transaction")
    print("2. View Transactions")
    print("3. Delete Transaction")
    print("4. Exit")

    choice = input("Enter your choice: ")

    if choice == "1":
        add_transaction()

    elif choice == "2":
        view_transactions()

    elif choice == "3":
        delete_transaction()

    elif choice == "4":
        print("Thank you for using UPI Expense Tracker!")

        # Close database connection
        connection.close()

        break

    else:
        print("Invalid choice. Please try again.")
