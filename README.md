# Countdown Timer Application with Arithmetic Calculator Feature

import time
import threading
import sqlite3

# SQLite Database Setup (Optional)
def setup_database():
    connection = sqlite3.connect("calculator_history.db")
    cursor = connection.cursor()
    cursor.execute("""CREATE TABLE IF NOT EXISTS history (
                        id INTEGER PRIMARY KEY AUTOINCREMENT,
                        operation TEXT,
                        result TEXT,
                        timestamp DATETIME DEFAULT CURRENT_TIMESTAMP
                    )"""
    )
    connection.commit()
    connection.close()

def save_to_database(operation, result):
    connection = sqlite3.connect("calculator_history.db")
    cursor = connection.cursor()
    cursor.execute("INSERT INTO history (operation, result) VALUES (?, ?)", (operation, result))
    connection.commit()
    connection.close()

# Countdown Timer
def countdown_timer(duration):
    try:
        print(f"Countdown started for {duration} seconds.")
        while duration:
            mins, secs = divmod(duration, 60)
            timeformat = '{:02d}:{:02d}'.format(mins, secs)
            print(timeformat, end='\r')
            time.sleep(1)
            duration -= 1
        print("Time's up!")
    except Exception as e:
        print(f"An error occurred: {e}")

# Calculator Functions
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

def multiply(a, b):
    return a * b

def divide(a, b):
    if b == 0:
        return "Error: Division by zero."
    return a / b

# Main Menu
def main_menu():
    print("Welcome to the Countdown Timer and Calculator Application")
    while True:
        print("\nMenu:")
        print("1. Countdown Timer")
        print("2. Calculator")
        print("3. View Calculation History (Optional)")
        print("4. Exit")

        choice = input("Enter your choice: ")

        if choice == '1':
            try:
                duration = int(input("Enter countdown duration in seconds: "))
                timer_thread = threading.Thread(target=countdown_timer, args=(duration,))
                timer_thread.start()
            except ValueError:
                print("Invalid input. Please enter an integer.")

        elif choice == '2':
            try:
                print("\nCalculator Menu:")
                print("1. Addition")
                print("2. Subtraction")
                print("3. Multiplication")
                print("4. Division")

                calc_choice = input("Enter your choice: ")
                num1 = float(input("Enter the first number: "))
                num2 = float(input("Enter the second number: "))

                if calc_choice == '1':
                    result = add(num1, num2)
                    print(f"The result is: {result}")
                    save_to_database(f"{num1} + {num2}", result)
                elif calc_choice == '2':
                    result = subtract(num1, num2)
                    print(f"The result is: {result}")
                    save_to_database(f"{num1} - {num2}", result)
                elif calc_choice == '3':
                    result = multiply(num1, num2)
                    print(f"The result is: {result}")
                    save_to_database(f"{num1} * {num2}", result)
                elif calc_choice == '4':
                    result = divide(num1, num2)
                    print(f"The result is: {result}")
                    save_to_database(f"{num1} / {num2}", result)
                else:
                    print("Invalid choice.")
            except ValueError:
                print("Invalid input. Please enter numeric values.")

        elif choice == '3':
            try:
                connection = sqlite3.connect("calculator_history.db")
                cursor = connection.cursor()
                cursor.execute("SELECT * FROM history")
                rows = cursor.fetchall()
                print("\nCalculation History:")
                for row in rows:
                    print(f"{row[1]} = {row[2]} (Timestamp: {row[3]})")
                connection.close()
            except Exception as e:
                print(f"An error occurred: {e}")

        elif choice == '4':
            print("Exiting the application. Goodbye!")
            break
        else:
            print("Invalid choice. Please try again.")

if __name__ == "__main__":
    setup_database()
    main_menu()# Countdown_Timer_Application-
