# CODSOFT
'''PASSWORD GENERATOR'''
from tkinter import *
import tkinter.messagebox as msg
import random
import string


class PasswordGenerator(Tk):
    def __init__(self):
        super().__init__()
        self.title("Password Generator")
        self.resizable(False, False)

        # Centering window
        app_width, app_height = 500, 280
        x = (self.winfo_screenwidth() - app_width) // 2
        y = (self.winfo_screenheight() - app_height) // 2
        self.geometry(f"{app_width}x{app_height}+{x}+{y}")

        self.choice = StringVar(value="high")

        # UI Setup
        self.setup_ui()

    def setup_ui(self):
        # Heading
        heading = Frame(self, bg="#2b2e30")
        heading.pack(fill=X)
        Label(heading, text="Password Generator", font=("Helvetica", 23, "bold"), fg="#b1ccea", bg="#2b2e30").pack()

        # Length Input
        length_frame = Frame(self, bg="#e5e7ea", pady=10, padx=20)
        length_frame.pack(fill=X)
        Label(length_frame, text="Set password length", font=("Helvetica", 16), bg="#e5e7ea", fg="#1a0944").pack(side=LEFT)
        self.length_entry = Entry(length_frame, width=5, font=("Helvetica", 13))
        self.length_entry.pack(side=LEFT, padx=(10, 0))

        # Strength Selection
        strength_frame = Frame(self, bg="#e5e7ea", pady=10, padx=20)
        strength_frame.pack(fill=X)
        Label(strength_frame, text="Set password strength", font=("Helvetica", 16), bg="#e5e7ea", fg="#1a0944").pack(anchor=W)
        for val in ["low", "medium", "high"]:
            Radiobutton(strength_frame, text=val, value=val, variable=self.choice, font=("Helvetica", 13), bg="#e5e7ea").pack(side=LEFT, padx=20)

        # Generate Button
        button_frame = Frame(self, bg="#e5e7ea", pady=10)
        button_frame.pack(fill=X)
        Button(button_frame, text="GENERATE", bg="#235d48", fg="#e1e6e4", font=("Helvetica", 13, "bold"),
               padx=20, pady=5, bd=0, cursor="hand2", command=self.handle_generate).pack()

    def handle_generate(self):
        try:
            length = int(self.length_entry.get().strip())
            if not 4 <= length <= 80:
                raise ValueError("Length must be between 4 and 80.")
            strength = self.choice.get()
            password = self.generate_password(length, strength)
            self.show_password(length, strength, password)
        except ValueError:
            msg.showwarning("Invalid Input", "Please enter a valid length between 4 and 80.")

    def generate_password(self, length, strength):
        if strength == "low":
            chars = string.ascii_letters
        elif strength == "medium":
            chars = string.ascii_letters + string.digits
        else:  # high
            chars = string.ascii_letters + string.digits + "!@#$%^&*"

        while True:
            password = ''.join(random.choice(chars) for _ in range(length))
            if strength == "medium" and not any(c.isdigit() for c in password):
                continue
            if strength == "high" and (not any(c.isdigit() for c in password) or not any(c in "!@#$%^&*" for c in password)):
                continue
            return password

    def show_password(self, length, strength, password):
        win = Toplevel(self)
        win.geometry("700x215")
        win.title("Generated Password")
        Label(win, text=f"Generated Password\nLength: {length}  Strength: {strength}",
              font=("Helvetica", 16), fg="#1d3b64").pack(pady=10)
        textbox = Text(win, height=3, width=70, font=("Helvetica", 13), fg="#1d3b64", bg="#e5e7ea")
        textbox.insert(END, password)
        textbox.config(state=DISABLED)
        textbox.pack()
        Button(win, text="Close", font=("Helvetica", 13, "bold"), bg="#3c8bdf", fg="#fff",
               width=13, bd=0, command=win.destroy).pack(side=RIGHT, padx=20, pady=10)

    def run(self):
        self.mainloop()


if __name__ == '__main__':
    PasswordGenerator().run()

''' Python Calculator'''
import tkinter as tk

class Calculator:
    def __init__(self, root):
        self.root = root
        self.root.title("Calculator")
        self.root.geometry("300x400")
        self.expression = ""
        
        self.text_input = tk.StringVar()
        
        # Entry widget to show expression/result
        self.entry = tk.Entry(root, textvariable=self.text_input, font=('arial', 20, 'bold'), bd=10, insertwidth=4, width=14, borderwidth=4, relief='ridge', justify='right')
        self.entry.grid(row=0, column=0, columnspan=4)
        
        # Buttons
        buttons = [
            ('7', 1, 0), ('8', 1, 1), ('9', 1, 2), ('/', 1, 3),
            ('4', 2, 0), ('5', 2, 1), ('6', 2, 2), ('*', 2, 3),
            ('1', 3, 0), ('2', 3, 1), ('3', 3, 2), ('-', 3, 3),
            ('0', 4, 0), ('.', 4, 1), ('C', 4, 2), ('+', 4, 3),
            ('=', 5, 0, 4)
        ]
        
        for (text, row, col, colspan) in [(*btn, 1) if len(btn) == 3 else btn for btn in buttons]:
            self.create_button(text, row, col, colspan)
        
    def create_button(self, text, row, col, colspan=1):
        button = tk.Button(self.root, text=text, width=7, height=3, font=('arial', 14),
                           command=lambda: self.button_click(text))
        button.grid(row=row, column=col, columnspan=colspan)
    
    def button_click(self, char):
        if char == 'C':
            self.expression = ""
            self.text_input.set(self.expression)
        elif char == '=':
            try:
                result = str(eval(self.expression))
                self.text_input.set(result)
                self.expression = result
            except Exception:
                self.text_input.set("Error")
                self.expression = ""
        else:
            self.expression += str(char)
            self.text_input.set(self.expression)

if __name__ == "__main__":
    root = tk.Tk()
    calc = Calculator(root)
    root.mainloop()

''' To-Do List'''
import tkinter as tk
from tkinter import messagebox, font

class ToDoApp:
    def __init__(self, root):
        self.root = root
        self.root.title("🎉 Vibrant To-Do List 🎉")
        self.root.geometry("450x400")
        self.root.config(bg="#282c34")  # Dark background

        self.tasks = []

        # Custom fonts
        self.title_font = font.Font(family="Helvetica", size=20, weight="bold")
        self.button_font = font.Font(family="Helvetica", size=12, weight="bold")
        self.list_font = font.Font(family="Helvetica", size=12)

        # Title Label
        self.title_label = tk.Label(
            root, text="Your To-Do List", 
            bg="#282c34", fg="#61dafb", font=self.title_font
        )
        self.title_label.pack(pady=(15, 5))

        # Frame for entry + button
        entry_frame = tk.Frame(root, bg="#282c34")
        entry_frame.pack(pady=10)

        # Task Entry
        self.task_entry = tk.Entry(entry_frame, width=30, font=self.list_font)
        self.task_entry.grid(row=0, column=0, padx=(0,10), ipady=6)

        # Add Task Button
        self.add_btn = tk.Button(
            entry_frame, text="Add Task", width=12, 
            bg="#61dafb", fg="#282c34", font=self.button_font,
            activebackground="#21a1f1", activeforeground="white",
            command=self.add_task
        )
        self.add_btn.grid(row=0, column=1)

        # Listbox with scrollbar
        list_frame = tk.Frame(root)
        list_frame.pack(pady=10)

        self.scrollbar = tk.Scrollbar(list_frame)
        self.scrollbar.pack(side=tk.RIGHT, fill=tk.Y)

        self.listbox = tk.Listbox(
            list_frame, width=45, height=12, font=self.list_font,
            yscrollcommand=self.scrollbar.set,
            bg="#20232a", fg="#61dafb", selectbackground="#21a1f1",
            selectforeground="white", relief=tk.FLAT, bd=0
        )
        self.listbox.pack()

        self.scrollbar.config(command=self.listbox.yview)

        # Delete Task Button
        self.del_btn = tk.Button(
            root, text="Delete Selected Task", width=20,
            bg="#e06c75", fg="white", font=self.button_font,
            activebackground="#be5046", activeforeground="white",
            command=self.delete_task
        )
        self.del_btn.pack(pady=15)

    def add_task(self):
        task = self.task_entry.get().strip()
        if task == "":
            messagebox.showwarning("Warning", "You must enter a task.")
        else:
            self.tasks.append(task)
            self.update_listbox()
            self.task_entry.delete(0, tk.END)

    def delete_task(self):
        try:
            selected_index = self.listbox.curselection()[0]
            self.tasks.pop(selected_index)
            self.update_listbox()
        except IndexError:
            messagebox.showwarning("Warning", "Please select a task to delete.")

    def update_listbox(self):
        self.listbox.delete(0, tk.END)
        for task in self.tasks:
            self.listbox.insert(tk.END, task)

if __name__ == "__main__":
    root = tk.Tk()
    app = ToDoApp(root)
    root.mainloop()
