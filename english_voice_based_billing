import sqlite3
import tkinter as tk
from tkinter import messagebox, ttk
import speech_recognition as sr
import re

# Database connection
def connect_db():
    conn = sqlite3.connect("store.db")
    cursor = conn.cursor()
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS products (
            product_id INTEGER PRIMARY KEY AUTOINCREMENT,
            name TEXT NOT NULL,
            qty INTEGER NOT NULL,
            price_per_kg_unit REAL NOT NULL
        )
    """)
    cursor.execute("""
        CREATE TABLE IF NOT EXISTS billing (
            bill_id INTEGER PRIMARY KEY AUTOINCREMENT,
            product TEXT NOT NULL,
            qty INTEGER NOT NULL,
            price_per_unit REAL NOT NULL,
            total_price REAL NOT NULL
        )
    """)
    conn.commit()
    conn.close()

# Add product
def add_product():
    name = name_entry.get().strip()
    qty = qty_entry.get().strip()
    price = price_entry.get().strip()
    
    if name and qty and price:
        conn = sqlite3.connect("store.db")
        cursor = conn.cursor()
        cursor.execute("INSERT INTO products (name, qty, price_per_kg_unit) VALUES (?, ?, ?)", (name, qty, price))
        conn.commit()
        conn.close()
        messagebox.showinfo("Success", "Product added successfully!")
        load_products()
    else:
        messagebox.showerror("Error", "All fields are required!")

# Load products
def load_products():
    conn = sqlite3.connect("store.db")
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM products")
    rows = cursor.fetchall()
    conn.close()
    
    product_tree.delete(*product_tree.get_children())
    for row in rows:
        product_tree.insert("", "end", values=row)

# Delete selected product from the products table
def delete_product():
    selected_item = product_tree.selection()
    if not selected_item:
        messagebox.showerror("Error", "Please select a product to delete.")
        return

    confirm = messagebox.askyesno("Confirm Delete", "Are you sure you want to delete the selected product?")
    if not confirm:
        return

    item = product_tree.item(selected_item)
    product_id = item["values"][0]

    conn = sqlite3.connect("store.db")
    cursor = conn.cursor()
    cursor.execute("DELETE FROM products WHERE product_id=?", (product_id,))
    conn.commit()
    conn.close()

    load_products()
    messagebox.showinfo("Success", "Product deleted successfully.")

# Add Billing Entry
def add_billing():
    product_name = billing_product_entry.get().strip()
    qty = billing_qty_entry.get().strip()

    if not product_name or not qty:
        messagebox.showerror("Error", "Please enter product and quantity!")
        return

    try:
        qty = int(qty)
    except ValueError:
        messagebox.showerror("Error", "Quantity must be a number!")
        return

    conn = sqlite3.connect("store.db")
    cursor = conn.cursor()
    cursor.execute("SELECT qty, price_per_kg_unit FROM products WHERE name=?", (product_name,))
    product = cursor.fetchone()
    
    if product:
        available_qty, price_per_unit = product
        if qty > available_qty:
            messagebox.showerror("Error", "Not enough stock available!")
        else:
            total_price = qty * price_per_unit
            cursor.execute("INSERT INTO billing (product, qty, price_per_unit, total_price) VALUES (?, ?, ?, ?)", 
                           (product_name, qty, price_per_unit, total_price))
            cursor.execute("UPDATE products SET qty=? WHERE name=?", (available_qty - qty, product_name))
            conn.commit()
            load_products()
            load_billing()
            messagebox.showinfo("Success", "Billing entry added and stock updated!")
    else:
        messagebox.showerror("Error", "Product not found!")
    
    conn.close()

# Load billing data and update total amount
def load_billing():
    conn = sqlite3.connect("store.db")
    cursor = conn.cursor()
    cursor.execute("SELECT * FROM billing")
    rows = cursor.fetchall()
    conn.close()
    
    billing_tree.delete(*billing_tree.get_children())
    total_amount = 0
    for row in rows:
        billing_tree.insert("", "end", values=row)
        total_amount += row[4]  # Total price column

    total_label.config(text=f"Total Bill: ₹{total_amount:.2f}")

# Complete Bill - Clears the current bill
def complete_bill():
    conn = sqlite3.connect("store.db")
    cursor = conn.cursor()
    cursor.execute("DELETE FROM billing")  # Clear the billing table
    conn.commit()
    conn.close()

    # Reset UI elements
    billing_tree.delete(*billing_tree.get_children())
    total_label.config(text="Total Bill: ₹0.00")

    messagebox.showinfo("Bill Completed", "New billing session started!")

# Process voice input for billing
def process_voice_input(text):
    match = re.match(r"(\d+)\s*(kg|grams|g|ml|liters|l|unit|units)?\s*(.*)", text, re.IGNORECASE)

    if match:
        qty = int(match.group(1))  # Extract quantity
        product_name = match.group(3).strip()  # Extract product name

        if not product_name:
            messagebox.showerror("Error", "Please specify a valid product name.")
            return

        billing_product_entry.delete(0, tk.END)
        billing_qty_entry.delete(0, tk.END)
        billing_product_entry.insert(0, product_name)
        billing_qty_entry.insert(0, str(qty))

        add_billing()
    else:
        messagebox.showerror("Error", "Invalid voice format. Try saying '1 kg rice'.")

# Voice recognition function
def voice_add_billing():
    recognizer = sr.Recognizer()
    with sr.Microphone() as source:
        messagebox.showinfo("Voice Input", "Speak the product and quantity (e.g., '1 kg rice').")
        recognizer.adjust_for_ambient_noise(source)
        try:
            audio = recognizer.listen(source)
            text = recognizer.recognize_google(audio)
            process_voice_input(text.lower())  # Convert to lowercase
        except sr.UnknownValueError:
            messagebox.showerror("Error", "Could not understand the audio.")
        except sr.RequestError:
            messagebox.showerror("Error", "Could not request results. Check internet connection.")

# GUI Setup
root = tk.Tk()
root.title("Product Management & Billing")
root.geometry("800x600")

# Product form
frame = tk.Frame(root)
frame.pack(pady=10)

tk.Label(frame, text="Name").grid(row=0, column=0)
name_entry = tk.Entry(frame)
name_entry.grid(row=0, column=1)

tk.Label(frame, text="Quantity").grid(row=1, column=0)
qty_entry = tk.Entry(frame)
qty_entry.grid(row=1, column=1)

tk.Label(frame, text="Price per KG/Unit").grid(row=2, column=0)
price_entry = tk.Entry(frame)
price_entry.grid(row=2, column=1)

tk.Button(frame, text="Add Product", command=add_product).grid(row=3, column=0)
tk.Button(frame, text="Load Products", command=load_products).grid(row=3, column=1)
tk.Button(frame, text="Delete Product", command=delete_product).grid(row=3, column=2)

# Product List
product_tree = ttk.Treeview(root, columns=("ID", "Name", "Qty", "Price per KG/Unit"), show="headings")
product_tree.heading("ID", text="ID")
product_tree.heading("Name", text="Name")
product_tree.heading("Qty", text="Quantity")
product_tree.heading("Price per KG/Unit", text="Price per KG/Unit")
product_tree.pack()

# Billing Section
tk.Label(root, text="Billing").pack()
billing_frame = tk.Frame(root)
billing_frame.pack(pady=10)

tk.Label(billing_frame, text="Product Name").grid(row=0, column=0)
billing_product_entry = tk.Entry(billing_frame)
billing_product_entry.grid(row=0, column=1)

tk.Label(billing_frame, text="Quantity").grid(row=1, column=0)
billing_qty_entry = tk.Entry(billing_frame)
billing_qty_entry.grid(row=1, column=1)

tk.Button(billing_frame, text="Add Billing", command=add_billing).grid(row=2, column=0)
tk.Button(billing_frame, text="Voice Add Billing", command=voice_add_billing).grid(row=2, column=1)

# Billing List
billing_tree = ttk.Treeview(root, columns=("ID", "Product", "Qty", "Price per Unit", "Total Price"), show="headings")
billing_tree.heading("ID", text="SL. No")
billing_tree.heading("Product", text="Product Name")
billing_tree.heading("Qty", text="Quantity")
billing_tree.heading("Price per Unit", text="Price per Unit")
billing_tree.heading("Total Price", text="Total Price")
billing_tree.pack()

# Total Bill Label
total_label = tk.Label(root, text="Total Bill: ₹0.00", font=("Arial", 12, "bold"))
total_label.pack()

# New "Complete Bill" Button
tk.Button(root, text="Complete Bill", command=complete_bill, bg="red", fg="white").pack(pady=10)

# Initialize
connect_db()
load_products()
load_billing()
root.mainloop()
