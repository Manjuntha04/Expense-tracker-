import json
from datetime import datetime
class ExpenseTracker:
    def __init__(self, filename="expenses.json"):
        self.filename = filename
        self.load_expenses()

    def load_expenses(self):
        """Loads expenses from a JSON file or creates a new file if it doesn't exist."""
        try:
            with open(self.filename, 'r') as file:
                self.expenses = json.load(file)
        except (FileNotFoundError, json.JSONDecodeError):
            self.expenses = []

    def save_expenses(self):
        """Saves expenses to a JSON file."""
        with open(self.filename, 'w') as file:
            json.dump(self.expenses, file, indent=4)

    def add_expense(self, amount, category, description=""):
        """Adds a new expense to the tracker."""
        try:
            expense = {
                "amount": float(amount),
                "category": category,
                "description": description,
                "date": datetime.now().strftime("%Y-%m-%d")
            }
            self.expenses.append(expense)
            self.save_expenses()
            print("Expense added successfully!")
        except ValueError:
            print("Error: Amount should be a number.")

    def view_expenses(self):
        """Displays all expenses."""
        for expense in self.expenses:
            print(f"{expense['date']} - {expense['category']}: ${expense['amount']} ({expense['description']})")

    def monthly_summary(self, month, year):
        """Displays the total expenses for a given month and year."""
        total = 0
        for expense in self.expenses:
            exp_date = datetime.strptime(expense["date"], "%Y-%m-%d")
            if exp_date.month == month and exp_date.year == year:
                total += expense["amount"]
        print(f"Total expenses for {month}/{year}: ${total}")

    def category_summary(self, category):
        """Displays total expenses by category."""
        total = sum(exp["amount"] for exp in self.expenses if exp["category"].lower() == category.lower())
        print(f"Total expenses for {category}: ${total}")

    def main_menu(self):
        """Main menu for the Expense Tracker."""
        while True:
            print("\nExpense Tracker Menu:")
            print("1. Add Expense")
            print("2. View Expenses")
            print("3. Monthly Summary")
            print("4. Category Summary")
            print("5. Exit")

            choice = input("Choose an option: ")
            if choice == "1":
                try:
                    amount = float(input("Enter amount: "))
                    category = input("Enter category (e.g., food, transportation): ")
                    description = input("Enter a description (optional): ")
                    self.add_expense(amount, category, description)
                except ValueError:
                    print("Invalid input. Please enter a number for the amount.")
            elif choice == "2":
                self.view_expenses()
            elif choice == "3":
                try:
                    month = int(input("Enter month (1-12): "))
                    year = int(input("Enter year (e.g., 2024): "))
                    self.monthly_summary(month, year)
                except ValueError:
                    print("Invalid input. Please enter a valid month and year.")
            elif choice == "4":
                category = input("Enter category: ")
                self.category_summary(category)
            elif choice == "5":
                print("Exiting Expense Tracker. Goodbye!")
                break
            else:
                print("Invalid choice. Please try again.")

# Running the Expense Tracker
if __name__ == "__main__":
    tracker = ExpenseTracker()
    tracker.main_menu()
