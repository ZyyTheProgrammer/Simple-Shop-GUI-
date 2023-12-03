# Simple-Shop-GUI-
1st year-BSCS (Fin-Lab_Act2)

from tkinter import *
from tkinter import messagebox


# I made the code to be consistent to the product's price.
# change the keys from the dictionary if u want to change the price of a specific item and will still work accurately.

items = {
    "Pencil": 15.00,
    "Pen": 25.00,
    "Yellow pad": 2.00,
    "Bond paper": 1.00,
    "Color": 45.50
}

quantity = 0
total = 0

def increase():
    global quantity, total
    quantity += 1
    lbl_quantity_amount.config(text=quantity)
    btn_decrease.config(state=ACTIVE)
    btn_AddToCart.config(state=ACTIVE)
    total = quantity * items.get(current_choose.get())
    lbl_total.config(text=total)
    if total == 0:
        lbl_total.config(text=0)


def decrease():
    global quantity, total
    quantity -= 1
    lbl_quantity_amount.config(text=quantity)
    total = quantity * items.get(current_choose.get())
    lbl_total.config(text=total)
    if total == 0:
        lbl_total.config(text=0) # added this so the 0.0 is gonna be 0
    if quantity == 0:
        btn_decrease.config(state=DISABLED)
        btn_AddToCart.config(state=DISABLED)


def reset(*args):
    lbl_price_amount.config(text=f"₱ {items.get(current_choose.get())}")
    global quantity, total
    quantity, total = 0, 0
    lbl_quantity_amount.config(text=0)
    if total == 0:
        lbl_total.config(text=0)
    if quantity == 0:
        btn_decrease.config(state=DISABLED)
        btn_AddToCart.config(state=DISABLED)

cart_list = []
def add_cart():
    # to not duplicate the same product if added to the cart but only to add the total
    if current_choose.get() in cart_list:
        cart_list[cart_list.index(current_choose.get())+1] += total
    else:
        cart_list.append(current_choose.get())
        cart_list.append(total)
    print (cart_list)
    reset()

def show_cart():
    if cart_list == []:
        messagebox.showerror(title="Empty", message="Cart is still Empty")
    else:
        global cart       # made this global variable because it can be destroyed outside of this function
        cart = Toplevel()
        cart.geometry("310x400")
        cart.resizable(False, False)
        cart.title("Cart")

        frame1 = Frame(cart, relief=SUNKEN, borderwidth=3)
        frame1.pack(pady=5, padx=5)
        lbl_frame1_product = Label(frame1, text="Product", font=("courier", 10, "bold"))
        lbl_frame1_product.grid(row=0, column=0, padx=5, pady=5)
        lbl_frame1_quantity = Label(frame1, text="Quantity", font=("courier", 10, "bold"))
        lbl_frame1_quantity.grid(row=0, column=1, padx=5, pady=5)
        lbl_frame1_price = Label(frame1, text="Price", font=("courier", 10, "bold"))
        lbl_frame1_price.grid(row=0, column=2, padx=5, pady=5)
        lbl_frame1_amount = Label(frame1, text="Amount", font=("courier", 10, "bold"))
        lbl_frame1_amount.grid(row=0, column=3, padx=5, pady=5)

        row_count = 1
        for i in range(0, len(cart_list), 2):  # only iterate even numbers (items from cart_list)
            product = cart_list[i]
            total_price = cart_list[i + 1]
            quantity = int(total_price / items.get(product))
            Label(frame1, text=product).grid(row=row_count, column=0, padx=5, pady=5)
            Label(frame1, text=quantity).grid(row=row_count, column=1, padx=5, pady=5)
            Label(frame1, text=items.get(product)).grid(row=row_count, column=2, padx=5, pady=5)
            Label(frame1, text=items.get(product) * quantity).grid(row=row_count, column=3, padx=5, pady=5)
            row_count += 1

        frame2 = Frame(cart, width=300, height=400)
        frame2.pack(pady=5, padx=5)

        f_subtotal = sum([int(value) for value in cart_list if isinstance(value, (int, float))])
        tax = f_subtotal * 0.1  # 10% tax

        global f_total          # I made this global because it will be used in getting the change
        f_total = f_subtotal + tax

        lbl_frame2_subtotal = Label(frame2, text="Subtotal:", font=("Arial", 10, "bold"))
        lbl_frame2_subtotal.place(x=150, y=10)
        lbl_frame2_tax = Label(frame2, text="10% Tax:", font=("Arial", 10, "bold"))
        lbl_frame2_tax.place(x=150, y=35)
        lbl_frame2_total = Label(frame2, text="Total:", font=("Arial", 10, "bold"))
        lbl_frame2_total.place(x=150, y=60)
        lbl_frame2_subtotal_amount = Label(frame2, text=f"₱ {f_subtotal:.2f}", font=("Arial", 10))
        lbl_frame2_subtotal_amount.place(x=220, y=10)
        lbl_frame2_tax_amount = Label(frame2, text=f"₱ {tax:.2f}", font=("Arial", 10))
        lbl_frame2_tax_amount.place(x=220, y=35)
        lbl_frame2_total_amount = Label(frame2, text=f"₱ {f_total:.2f}", font=("Arial", 10))
        lbl_frame2_total_amount.place(x=220, y=60)


        def show_change():
            def is_valid_float(x):  # made my own function that return True or False if the string can be float
                try:  # this is like .isdigit() but that function return False if the string of numbers has decimal
                    float(x)
                    return True
                except ValueError:
                    return False

            entry = entry_cash.get()
            if is_valid_float(entry):
                entry = float(entry)
                if entry > f_total:
                    global change
                    change = f"₱ {entry - f_total:.2f}"
                    lbl_change_amount.config(text=change)

                else:
                    messagebox.showerror(title="Value Error", message="Input must be greater than the total expenses")
            else:
                messagebox.showerror(title="Value Error", message="Input must be in a Number")

        lbl_cash = Label(cart, text="Cash:", font=("Arial", 10, "bold"))
        lbl_cash.place(x=25, y=320)
        entry_cash = Entry(cart, justify="center")
        entry_cash.place(x=100, y=320)
        lbl_change = Label(cart, text="Change: ", font=("Arial", 10, "bold"))
        lbl_change.place(x=25, y=350)
        lbl_change_amount = Label(cart, text="", font=("Arial", 10))
        lbl_change_amount.place(x=130, y=350)
        btn_ok = Button(cart, text="OK", command=show_change)
        btn_ok.place(x=250, y=316)

        cart.mainloop()

def Empty_cart():
    cart_list.clear()
    cart.destroy()


window = Tk()
window.geometry("400x500")
window.title("Lazada.prototype")
window.resizable(False, False)

btn_cart = Button(window, text="Cart", font=("Arial", 10, "bold"), width=5, command=show_cart)
btn_cart.place(x=324, y=20)

frame_product = LabelFrame(window, text="Select Product", font=("Arial", 10, "bold"), width=350, height=55)
frame_product.pack_propagate(False)
frame_product.place(x=25, y=50)

current_choose = StringVar()
current_choose.set("Pencil")
current_choose.trace_add("write", reset)
option_product = OptionMenu(frame_product, current_choose, "Pencil", "Pen", "Yellow pad", "Bond paper", "Color")
option_product.config(width=13)
option_product.pack()


frame_quantity = LabelFrame(window, width=350, height=150)
frame_quantity.pack_propagate(False)
frame_quantity.place(x=25, y=120)

lbl_price = Label(frame_quantity, text="Price:", font=("Arial", 10))
lbl_price.place(x=10, y=10)
lbl_price_amount = Label(frame_quantity, text=f"₱ {items.get(current_choose.get())}")
lbl_price_amount.pack(pady=15)

lbl_quantity = Label(frame_quantity, text="Quantity:", font=("Arial", 10))
lbl_quantity.place(x=10, y=60)
btn_decrease = Button(frame_quantity, text="–", font=("Arial", 10, "bold"), width=3, state=DISABLED, command=decrease)
btn_decrease.place(x=112, y=60)
btn_increase = Button(frame_quantity, text="+", font=("Arial", 10), width=3, command=increase)
btn_increase.place(x=200, y=60)
lbl_quantity_amount = Label(frame_quantity, text=quantity, font=("Arial", 10, "bold"))
lbl_quantity_amount.pack(pady=10)

lbl_quantity = Label(frame_quantity, text="Total:", font=("Arial", 10))
lbl_quantity.place(x=10, y=110)
lbl_total = Label(frame_quantity, text=0, font=("Arial", 10, "bold"))
lbl_total.pack(pady=10)

btn_AddToCart = Button(window, text="Add to Cart", font=("Arial", 10, "bold"), command=add_cart, state=DISABLED)
btn_AddToCart.place(x=160, y=280)

btn_EmptyCart = Button(window, text="Reset / Empty Cart", font=("Arial", 10), command=Empty_cart)
btn_EmptyCart.place(x=25, y=450)

window.mainloop()
