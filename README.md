import datetime
import json
import os

class SimpleLifeTracker:
    def __init__(self):
        self.data_file = "life_tracker_data.json"
        self.load_data()
    
    def load_data(self):
        if os.path.exists(self.data_file):
            with open(self.data_file, 'r') as file:
                self.data = json.load(file)
        else:
            self.data = {
                "habits": [],
                "expenses": [],
                "daily_notes": []
            }
            self.save_data()
    
    def save_data(self):
        with open(self.data_file, 'w') as file:
            json.dump(self.data, file, indent=2)
    
    def get_time_of_day(self):
        current_hour = datetime.datetime.now().hour
        
        if 5 <= current_hour < 12:
            return "morning"
        elif 12 <= current_hour < 17:
            return "afternoon"
        elif 17 <= current_hour < 22:
            return "evening"
        else:
            return "night"
    
    def get_habit_categories(self):
        return {
            "health": [
                "Morning exercise", "Evening workout", "10k steps", "Stretching",
                "Drink water", "Take vitamins", "Healthy breakfast", "No junk food",
                "Early bedtime", "7+ hours sleep"
            ],
            "productivity": [
                "Morning planning", "Most important task", "No social media before work",
                "Pomodoro technique", "Clear desk", "Email management", 
                "Learn something new", "Review goals", "Evening reflection", "Plan tomorrow"
            ],
            "wellness": [
                "Meditation", "Journaling", "Gratitude practice", "Digital detox",
                "Reading", "Time in nature", "Deep breathing", "Positive affirmations",
                "Listen to music", "Call a friend"
            ],
            "money": [
                "Track expenses", "Save money", "No impulse buys", "Cook at home",
                "Compare prices", "Review budget", "Invest learning", "Side income work",
                "Cancel subscriptions", "Financial planning"
            ]
        }
    
    def get_time_specific_habits(self, time_of_day):
        time_habits = {
            "morning": [
                "Morning exercise", "Meditation", "Healthy breakfast", 
                "Planning day", "Drink water", "Make bed"
            ],
            "afternoon": [
                "Lunch walk", "Hydration check", "Pomodoro session",
                "Learn something", "Stretching", "Healthy snack"
            ],
            "evening": [
                "Evening workout", "Reading", "Family time",
                "Digital detox", "Gratitude journal", "Plan tomorrow"
            ],
            "night": [
                "No screens 1hr", "Reading", "Reflection",
                "Prepare for tomorrow", "Skincare", "Early bedtime"
            ]
        }
        return time_habits.get(time_of_day, [])
    
    def show_habit_categories(self):
        categories = self.get_habit_categories()
        
        print("\n--- Habit Categories ---")
        print("1. Health & Fitness")
        print("2. Productivity")
        print("3. Mental Wellness") 
        print("4. Financial Habits")
        print("5. Time-based suggestions")
        print("6. Custom Habit")
        
        choice = input("Choose category (1-6): ")
        
        if choice == "1":
            return categories["health"]
        elif choice == "2":
            return categories["productivity"]
        elif choice == "3":
            return categories["wellness"]
        elif choice == "4":
            return categories["money"]
        elif choice == "5":
            time_of_day = self.get_time_of_day()
            return self.get_time_specific_habits(time_of_day)
        elif choice == "6":
            return []
        else:
            print("Invalid choice")
            return []
    
    def get_suggestions(self, time_of_day):
        suggestions = {
            "morning": {
                "habits": self.get_time_specific_habits("morning"),
                "expenses": ["coffee", "breakfast", "transport"]
            },
            "afternoon": {
                "habits": self.get_time_specific_habits("afternoon"),
                "expenses": ["lunch", "snacks", "work supplies"]
            },
            "evening": {
                "habits": self.get_time_specific_habits("evening"),
                "expenses": ["groceries", "dinner", "entertainment"]
            },
            "night": {
                "habits": self.get_time_specific_habits("night"),
                "expenses": ["snacks", "self care"]
            }
        }
        return suggestions.get(time_of_day, {"habits": [], "expenses": []})
    
    def show_current_context(self):
        time_of_day = self.get_time_of_day()
        current_time = datetime.datetime.now().strftime("%H:%M")
        
        print("\n" + "="*50)
        print("SIMPLE LIFE TRACKER")
        print("="*50)
        print("Current Time: " + current_time)
        print("Time of Day: " + time_of_day)
        print("-"*50)
        
        suggestions = self.get_suggestions(time_of_day)
        
        print("Suggested Habits:")
        for habit in suggestions["habits"]:
            print(" • " + habit)
        
        print("\nCommon Expenses:")
        for expense in suggestions["expenses"]:
            print(" • " + expense)
        print("="*50)
    
    def add_habit(self):
        time_of_day = self.get_time_of_day()
        current_date = datetime.date.today().isoformat()
        
        print("\n--- Add Habit ---")
        
        # Show habit categories
        habit_list = self.show_habit_categories()
        
        if habit_list:  # Predefined habits
            print("\nChoose a habit:")
            for i, habit in enumerate(habit_list, 1):
                print(str(i) + ". " + habit)
            
            try:
                choice = int(input("Select habit (1-" + str(len(habit_list)) + "): "))
                if 1 <= choice <= len(habit_list):
                    habit_name = habit_list[choice - 1]
                else:
                    print("Invalid selection")
                    return
            except ValueError:
                print("Please enter a number")
                return
        else:  # Custom habit
            habit_name = input("Enter your custom habit: ")
        
        completed = input("Did you complete it? (yes/no): ").lower()
        notes = input("Any notes? (optional): ")
        
        habit_data = {
            "date": current_date,
            "time_of_day": time_of_day,
            "name": habit_name,
            "completed": completed == "yes",
            "notes": notes
        }
        
        self.data["habits"].append(habit_data)
        self.save_data()
        print("Habit '" + habit_name + "' saved!")
    
    def add_expense(self):
        time_of_day = self.get_time_of_day()
        current_date = datetime.date.today().isoformat()
        
        print("\n--- Add Expense ---")
        category = input("What category? (food, transport, entertainment, etc.): ")
        
        try:
            amount = float(input("How much did you spend? "))
            description = input("What was it for? ")
        except:
            print("Invalid amount!")
            return
        
        expense_data = {
            "date": current_date,
            "time_of_day": time_of_day,
            "category": category,
            "amount": amount,
            "description": description
        }
        
        self.data["expenses"].append(expense_data)
        self.save_data()
        print("Expense saved!")
    
    def add_note(self):
        time_of_day = self.get_time_of_day()
        current_date = datetime.date.today().isoformat()
        
        print("\n--- Add Note ---")
        note = input("How are you feeling? ")
        
        note_data = {
            "date": current_date,
            "time_of_day": time_of_day,
            "note": note
        }
        
        self.data["daily_notes"].append(note_data)
        self.save_data()
        print("Note saved!")
    
    def show_today_summary(self):
        current_date = datetime.date.today().isoformat()
        
        # Get today's habits
        today_habits = [h for h in self.data["habits"] if h["date"] == current_date]
        
        # Get today's expenses
        today_expenses = [e for e in self.data["expenses"] if e["date"] == current_date]
        
        print("\n" + "="*50)
        print("TODAY'S SUMMARY")
        print("="*50)
        
        # Habits summary
        if today_habits:
            completed = sum(1 for h in today_habits if h["completed"])
            total = len(today_habits)
            percentage = (completed / total * 100) if total > 0 else 0
            print("Habits: " + str(completed) + "/" + str(total) + " completed (" + str(round(percentage)) + "%)")
            
            print("\nYour habits today:")
            for habit in today_habits:
                status = "✓ DONE" if habit["completed"] else "✗ NOT DONE"
                print(" " + status + " - " + habit["name"] + " (" + habit["time_of_day"] + ")")
                if habit.get("notes"):
                    print("     Note: " + habit["notes"])
        else:
            print("No habits tracked today")
        
        # Expenses summary
        if today_expenses:
            total_spent = sum(e["amount"] for e in today_expenses)
            print("\nExpenses: $" + str(round(total_spent, 2)))
            
            print("\nYour spending today:")
            for expense in today_expenses:
                print(" $" + str(expense["amount"]) + " - " + expense["category"] + " (" + expense["time_of_day"] + ")")
                if expense["description"]:
                    print("     " + expense["description"])
        else:
            print("\nNo expenses tracked today")
        
        print("="*50)
    
    def show_habit_stats(self):
        current_date = datetime.date.today().isoformat()
        today_habits = [h for h in self.data["habits"] if h["date"] == current_date]
        
        if not today_habits:
            print("\nNo habits tracked today!")
            return
        
        completed = sum(1 for h in today_habits if h["completed"])
        total = len(today_habits)
        percentage = (completed / total * 100) if total > 0 else 0
        
        print("\n" + "="*50)
        print("HABIT STATS")
        print("="*50)
        print("Today's Completion: " + str(completed) + "/" + str(total) + " (" + str(round(percentage)) + "%)")
        
        # Completion by time of day
        time_stats = {}
        for habit in today_habits:
            time = habit["time_of_day"]
            if time not in time_stats:
                time_stats[time] = {"total": 0, "completed": 0}
            time_stats[time]["total"] += 1
            if habit["completed"]:
                time_stats[time]["completed"] += 1
        
        if time_stats:
            print("\nBy Time of Day:")
            for time, stats in time_stats.items():
                rate = (stats["completed"] / stats["total"] * 100) if stats["total"] > 0 else 0
                print(" " + time + ": " + str(stats["completed"]) + "/" + str(stats["total"]) + " (" + str(round(rate)) + "%)")
        
        print("="*50)
    
    def show_habit_streaks(self):
        # Count how many times each habit appears
        habit_counts = {}
        completed_counts = {}
        
        for habit in self.data["habits"]:
            name = habit["name"]
            if name in habit_counts:
                habit_counts[name] += 1
                if habit["completed"]:
                    completed_counts[name] += 1
            else:
                habit_counts[name] = 1
                completed_counts[name] = 1 if habit["completed"] else 0
        
        if habit_counts:
            print("\n" + "="*50)
            print("HABIT FREQUENCY")
            print("="*50)
            
            # Sort by most frequent
            sorted_habits = sorted(habit_counts.items(), key=lambda x: x[1], reverse=True)
            
            print("Most tracked habits:")
            for habit, count in sorted_habits[:8]:  # Top 8
                completed = completed_counts[habit]
                completion_rate = (completed / count * 100) if count > 0 else 0
                print(" • " + habit + ": " + str(count) + " times (" + str(round(completion_rate)) + "% done)")
            
            print("="*50)
        else:
            print("\nNo habit data yet. Start tracking to see your streaks!")
    
    def show_simple_insights(self):
        print("\n" + "="*50)
        print("SIMPLE INSIGHTS")
        print("="*50)
        
        # Habit insights
        if self.data["habits"]:
            total_habits = len(self.data["habits"])
            completed_habits = sum(1 for h in self.data["habits"] if h["completed"])
            overall_rate = (completed_habits / total_habits * 100) if total_habits > 0 else 0
            
            print("Overall Habit Completion: " + str(round(overall_rate)) + "%")
            print("Total habits tracked: " + str(total_habits))
        
        # Expense insights
        if self.data["expenses"]:
            total_spent = sum(e["amount"] for e in self.data["expenses"])
            avg_daily = total_spent / len(set(e["date"] for e in self.data["expenses"]))
            
            print("\nTotal spent: $" + str(round(total_spent, 2)))
            print("Average daily: $" + str(round(avg_daily, 2)))
            
            # Most common expense category
            category_totals = {}
            for expense in self.data["expenses"]:
                category = expense["category"]
                if category in category_totals:
                    category_totals[category] += expense["amount"]
                else:
                    category_totals[category] = expense["amount"]
            
            if category_totals:
                top_category = max(category_totals, key=category_totals.get)
                print("You spend most on: " + top_category)
        
        print("="*50)
    
    def run(self):
        print("Welcome to Simple Life Tracker!")
        print("Track your habits and expenses throughout the day!")
        
        while True:
            self.show_current_context()
            
            print("\nWhat would you like to do?")
            print("1. Add a habit (with categories)")
            print("2. Add an expense") 
            print("3. Add a note")
            print("4. View today's summary")
            print("5. View habit stats")
            print("6. View habit frequency") 
            print("7. See simple insights")
            print("8. Exit")
            
            choice = input("\nChoose 1-8: ")
            
            if choice == "1":
                self.add_habit()
            elif choice == "2":
                self.add_expense()
            elif choice == "3":
                self.add_note()
            elif choice == "4":
                self.show_today_summary()
            elif choice == "5":
                self.show_habit_stats()
            elif choice == "6":
                self.show_habit_streaks()
            elif choice == "7":
                self.show_simple_insights()
            elif choice == "8":
                print("\nGoodbye! Your data is saved.")
                break
            else:
                print("Please choose 1-8")

# Run the program
if __name__ == "__main__":
    tracker = SimpleLifeTracker()
    tracker.run()
