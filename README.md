import tkinter as tk
from tkinter import messagebox
import pickle
import os

class FoodDeliveryApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Food Delivery System")
        self.root.geometry("600x700")
        self.root.configure(bg='#f0f0f0')

        self.orders = self.load_orders()  # Load saved orders if available
        self.create_widgets()

    def load_orders(self):
        # Load saved orders from file if it exists
        if os.path.exists("orders.pkl"):
            with open("orders.pkl", "rb") as file:
                return pickle.load(file)
        return []  # Return an empty list if no saved orders

    def save_orders(self):
        # Save orders to a file
        with open("orders.pkl", "wb") as file:
            pickle.dump(self.orders, file)

    def create_widgets(self):
        # Title Label
        self.title_label = tk.Label(self.root, text="🍔 Food Delivery System 🍕", font=("Arial", 20, 'bold'), fg="#ff6347", bg="#f0f0f0")
        self.title_label.pack(pady=20)

        # Menu Frame
        self.menu_frame = tk.Frame(self.root, bg="#f0f0f0")
        self.menu_frame.pack(fill="x", pady=10)

        # Menu Items
        self.menu_label = tk.Label(self.menu_frame, text="🍴 Select Menu", font=("Arial", 14, 'bold'), fg="#333", bg="#f0f0f0")
        self.menu_label.grid(row=0, column=0, padx=10, pady=5)

        # Predefined menu items to be selected
        self.menu_items = [
            "Pizza - $10", "Burger - $5", "Pasta - $8", "Sushi - $12", "Salad - $7", 
            "Tacos - $6", "Steak - $15", "Fries - $4", "Ice Cream - $3", "Gujarati Thali - $12", 
            "Punjabi Thali - $14", "Fast Food - $8"
        ]

        # Dropdown for selecting menu items
        self.selected_items = []

        self.selected_item_var = tk.StringVar(self.root)
        self.selected_item_var.set(self.menu_items[0])  # Default value

        # OptionMenu to select food items
        self.food_dropdown = tk.OptionMenu(self.menu_frame, self.selected_item_var, *self.menu_items)
        self.food_dropdown.grid(row=1, column=0, padx=10, pady=5)

        # Button to add selected item to the order
        self.add_item_button = tk.Button(self.menu_frame, text="Add Item", command=self.add_item, font=("Arial", 12))
        self.add_item_button.grid(row=2, column=0, padx=10, pady=5)

        # Customer Details Section
        self.details_frame = tk.Frame(self.root, bg="#f0f0f0")
        self.details_frame.pack(pady=20)

        self.name_label = tk.Label(self.details_frame, text="Name:", font=("Arial", 12), bg="#f0f0f0")
        self.name_label.grid(row=0, column=0, padx=10, pady=5)
        self.name_entry = tk.Entry(self.details_frame, font=("Arial", 12))
        self.name_entry.grid(row=0, column=1, padx=10, pady=5)

        self.address_label = tk.Label(self.details_frame, text="Address:", font=("Arial", 12), bg="#f0f0f0")
        self.address_label.grid(row=1, column=0, padx=10, pady=5)
        self.address_entry = tk.Entry(self.details_frame, font=("Arial", 12))
        self.address_entry.grid(row=1, column=1, padx=10, pady=5)

        self.phone_label = tk.Label(self.details_frame, text="Phone Number:", font=("Arial", 12), bg="#f0f0f0")
        self.phone_label.grid(row=2, column=0, padx=10, pady=5)
        self.phone_entry = tk.Entry(self.details_frame, font=("Arial", 12))
        self.phone_entry.grid(row=2, column=1, padx=10, pady=5)

        # Order Button
        self.order_button = tk.Button(self.root, text="📦 Place Order", font=("Arial", 14), bg="#4CAF50", fg="white", command=self.place_order)
        self.order_button.pack(pady=20)

        # Order Tracking Section
        self.track_frame = tk.Frame(self.root, bg="#f0f0f0")
        self.track_frame.pack(pady=20)

        self.track_label = tk.Label(self.track_frame, text="🔍 Track Order (Enter Name):", font=("Arial", 12), bg="#f0f0f0")
        self.track_label.grid(row=0, column=0, padx=10, pady=5)
        self.track_entry = tk.Entry(self.track_frame, font=("Arial", 12))
        self.track_entry.grid(row=0, column=1, padx=10, pady=5)

        self.track_button = tk.Button(self.root, text="🔍 Track", font=("Arial", 14), bg="#2196F3", fg="white", command=self.track_order)
        self.track_button.pack(pady=20)

        # Results Display
        self.results_frame = tk.Frame(self.root, bg="#f0f0f0")
        self.results_frame.pack(pady=10)

        self.results_text = tk.Text(self.results_frame, height=10, width=60, wrap="word", font=("Courier", 12))
        self.results_text.pack(side="left", fill="both", expand=True)

        self.scrollbar = tk.Scrollbar(self.results_frame, orient="vertical", command=self.results_text.yview)
        self.scrollbar.pack(side="right", fill="y")
        self.results_text.configure(yscrollcommand=self.scrollbar.set)

    def add_item(self):
        # Add selected item to the order
        selected_item = self.selected_item_var.get()
        if selected_item:
            self.selected_items.append(selected_item)
            messagebox.showinfo("Item Added", f"{selected_item} has been added to your order.")
        else:
            messagebox.showwarning("Warning", "Please select an item.")

    def place_order(self):
        # Get customer name, address, and phone number
        name = self.name_entry.get().strip()
        address = self.address_entry.get().strip()
        phone = self.phone_entry.get().strip()

        if not name or not address or not phone:
            messagebox.showwarning("Warning", "Please enter name, address, and phone number.")
            return

        # If no items are selected, show a warning
        if not self.selected_items:
            messagebox.showwarning("Warning", "Please add at least one item to your order.")
            return

        order_details = f"Order for {name} (Phone: {phone}) at {address}:\n"
        order_details += "\n".join(self.selected_items)
        self.orders.append(order_details)

        messagebox.showinfo("Order Placed", f"Your order has been placed!\n{order_details}")

        # Save orders to file
        self.save_orders()

        # Clear customer details and selected items
        self.name_entry.delete(0, tk.END)
        self.address_entry.delete(0, tk.END)
        self.phone_entry.delete(0, tk.END)
        self.selected_items.clear()

    def track_order(self):
        # Get the customer name to track order
        name = self.track_entry.get().strip()
        if not name:
            messagebox.showwarning("Warning", "Please enter a name to track the order.")
            return

        found_orders = [order for order in self.orders if name.lower() in order.lower()]
        if found_orders:
            self.results_text.delete(1.0, tk.END)
            self.results_text.insert(tk.END, "\n".join(found_orders))
        else:
            messagebox.showinfo("No Orders", f"No orders found for {name}.")

if __name__ == "__main__":
    root = tk.Tk()
    app = FoodDeliveryApp(root)
    root.mainloop()

