# Build-a-Budget-App-by-Python
# Budget App in Python is a beginner-friendly project that helps users manage their income, expenses, # and savings. It allows tracking monthly budgets, categorizing expenses, and calculating remaining # balances. This project is great for practicing Python basics, conditional logic, and file handling, # while building something practical and useful.

class Category:
    def __init__(self, name):
        self.name = name
        self.ledger = []

    def deposit(self, amount, description=""):
        self.ledger.append({"amount": amount, "description": description})

    def withdraw(self, amount, description=""):
        if self.check_funds(amount):
            self.ledger.append({"amount": -amount, "description": description})
            return True
        return False

    def get_balance(self):
        return sum(item["amount"] for item in self.ledger)

    def transfer(self, amount, category_instance):
        if self.check_funds(amount):
            self.withdraw(amount, f"Transfer to {category_instance.name}")
            category_instance.deposit(amount, f"Transfer from {self.name}")
            return True
        return False

    def check_funds(self, amount):
        return amount <= self.get_balance()

    def __str__(self):
        title = self.name.center(30, "*") + "\n"
        entries = ""
        for item in self.ledger:
            desc = item["description"][:23].ljust(23)
            amt = f"{item['amount']:.2f}".rjust(7)
            entries += f"{desc}{amt}\n"
        total = f"Total: {self.get_balance():.2f}"
        
        return title + entries + total


def create_spend_chart(categories):
    spent_per_category = []
    for cat in categories:
        spent = sum(-item["amount"] for item in cat.ledger if item["amount"] < 0)
        spent_per_category.append(spent)
        
    total_spent = sum(spent_per_category)
    percentages = []
    for spent in spent_per_category:
        if total_spent == 0:
            percentages.append(0)
        else:
            percentages.append(int((spent / total_spent) * 100 // 10) * 10)
    chart = "Percentage spent by category\n"
    for i in range(100, -1, -10,):
        chart += f"{str(i).rjust(3)}| "
        for pct in percentages:
            if pct >= i:
                chart += "o  "
            else:
                chart += "   "
        chart += "\n"
    chart += "    " + "-" * (len(categories) * 3 + 1) + "\n"
    
    max_len = max(len(cat.name) for cat in categories)
    padded_names = [cat.name.ljust(max_len) for cat in categories]
    
    for i in range(max_len):
        chart += "     "
        for name in padded_names:
            chart += name[i] + "  "
        if i < max_len - 1:
            chart += "\n" 
    return chart
