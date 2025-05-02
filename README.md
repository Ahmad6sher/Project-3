# Project3SHER

## Project 3 — TU Vending Machine  
This Python project simulates a TU vending machine after a ransomware wipe. It reads JSON inventory, lets users purchase snacks, saves a transaction log, and supports extra features like currency switching and inventory charting.

---

## Functions

### `load_inventory()`  
Loads inventory from `tu_vending_inventory.json`, assigning grid slots (A1–F5).

```python
import json

def load_inventory():
    with open("tu_vending_inventory.json", "r") as f:
        data = json.load(f)["inventory"]
    slots = [f"{r}{c+1}" for r in "ABCDEF" for c in range(5)]
    return {slot: dict(item, slot=slot) for slot, item in zip(slots, data)}

display_inventory(inv, currency)
Displays all in-stock items with prices in the current currency.

def display_inventory(inv, currency):
    for slot, item in inv.items():
        if item["quantity"] > 0:
            price = round(item["price_usd"] * currency["rate"], 2)
            print(f"{slot} {currency['symbol']}{price} {item['item']}")
purchase_item(inv, slot, transactions)
Handles vending: reduces stock, logs transaction, and returns item price.

def purchase_item(inv, slot, transactions):
    item = inv.get(slot)
    if item and item["quantity"] > 0:
        item["quantity"] -= 1
        transactions.append({"SLOT_ID": slot, "ITEM": item["item"], "AMOUNT_USD": item["price_usd"]})
        return item["price_usd"]
    return 0.0
save_inventory(inv)
Writes the updated inventory to a file (for next run).

def save_inventory(inv):
    data = {"inventory": [v for v in inv.values()]}
    with open("ending_inventory.json", "w") as f:
        json.dump(data, f, indent=2)
save_transactions(transactions)
Saves the log of purchases to a CSV file.

import csv

def save_transactions(transactions):
    with open("transactions.csv", "w", newline="") as f:
        writer = csv.DictWriter(f, fieldnames=["SLOT_ID", "AMOUNT_USD", "ITEM"])
        writer.writeheader()
        writer.writerows(transactions)
generate_chart(inv)
Creates a bar chart showing stock quantities.

import matplotlib.pyplot as plt

def generate_chart(inv):
    items = [v["item"] for v in inv.values()]
    quantities = [v["quantity"] for v in inv.values()]
    plt.bar(items, quantities)
    plt.xticks(rotation=90)
    plt.title("Vending Inventory Levels")
    plt.tight_layout()
    plt.savefig("inventory_chart.png")
    plt.close()
How to Run
Install Required Libraries

pip install matplotlib
Run the Script

python3 vending.py
Unit Tests
test_price_conversion()
Tests correct price conversion from USD to other currencies.

test_purchase_decrements_inventory()
Checks if quantity updates correctly after a purchase.

python3 -m unittest vending.py
What Went Wrong
FileNotFoundError due to missing or misnamed JSON

Early input errors (slot not found or lowercase mismatch)

matplotlib not installed on first run

How I Fixed It
Added .strip().upper() for cleaner input handling

Checked all file paths and structured inventory as a dictionary

Included pip install matplotlib in the setup instructions

What I’d Do Next Time
Add SQLite support instead of JSON/CSV

Add a GUI using Tkinter

Connect to a real exchange rate API (live conversion)

Use a class-based OOP design for VendingMachine

Files

vending.py                # Main code
tu_vending_inventory.json # Initial inventory
ending_inventory.json     # Saved inventory after quit
transactions.csv          # Log of purchases
inventory_chart.png       # Optional chart image

## Notes

- Be careful with the JSON file format — one typo can stop it from loading  
- Slot input should be uppercase or it won’t match (use `.upper()`)  
- Chart needs matplotlib installed to work  
- Out-of-stock items still show in the display  
- Currency feature uses preset rates, not live ones  
