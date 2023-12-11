import tkinter as tk
from tkinter import ttk
import sqlite3
import math  # Importing math for square root operation

# Initialize the database
conn = sqlite3.connect('calculations.db')
cursor = conn.cursor()
cursor.execute('''
CREATE TABLE IF NOT EXISTS calculations (
    id INTEGER PRIMARY KEY,
    operation TEXT,
    number1 REAL,
    number2 REAL,
    result REAL
)
''')
conn.commit()

# Custom function to perform calculation and save result to database
def calculate_and_save(operation, num1, num2):
    result = None
    if operation == 'Add':
        result = num1 + num2
    elif operation == 'Subtract':
        result = num1 - num2
    elif operation == 'Multiply':
        result = num1 * num2
    elif operation == 'Divide':
        result = num1 / num2
    elif operation == 'Exponent':
        result = num1 ** num2
    elif operation == 'Modulo':
        result = num1 % num2
    elif operation == 'Square Root':
        result = math.sqrt(num1)  # num2 is ignored for this operation

    # Save to database
    cursor.execute('INSERT INTO calculations (operation, number1, number2, result) VALUES (?, ?, ?, ?)', 
                   (operation, num1, num2, result))
    conn.commit()

    return result

# Setting up the main window
root = tk.Tk()
root.title("Advanced Calculator")

# Function to take inputs and display result
def on_calculate():
    try:
        num1 = float(number1_entry.get())
        # For square root, the second number is not needed
        num2 = float(number2_entry.get()) if operation_combobox.get() != 'Square Root' else 0
        operation = operation_combobox.get()
        result = calculate_and_save(operation, num1, num2)
        result_label.config(text=f'Result: {result}')
    except ValueError:
        result_label.config(text="Invalid input, please enter numeric values.")

# Creating widgets
number1_label = ttk.Label(root, text="Number 1:")
number1_label.pack()
number1_entry = ttk.Entry(root)
number1_entry.pack()

number2_label = ttk.Label(root, text="Number 2 (not needed for Square Root):")
number2_label.pack()
number2_entry = ttk.Entry(root)
number2_entry.pack()

operation_label = ttk.Label(root, text="Operation:")
operation_label.pack()
operation_combobox = ttk.Combobox(root, values=["Add", "Subtract", "Multiply", "Divide", "Exponent", "Modulo", "Square Root"])
operation_combobox.pack()

calculate_button = ttk.Button(root, text="Calculate", command=on_calculate)
calculate_button.pack()

result_label = ttk.Label(root, text="Result: ")
result_label.pack()

# Ensure to close the database connection when the app is closed
def on_closing():
    if conn:
        conn.close()
    root.destroy()

root.protocol("WM_DELETE_WINDOW", on_closing)

# Run the application
root.mainloop()




