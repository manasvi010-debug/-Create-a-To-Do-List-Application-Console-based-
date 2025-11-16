# -Create-a-To-Do-List-Application-Console-based-
# Task Management System Implementation

class TaskManager:
    """
    A simple task management system that stores tasks in a list and persists them to a file.
    """
    
    def __init__(self, filename='tasks.txt'):
        """
        Initialize the TaskManager with a filename for data persistence.
        
        Args:
            filename (str): Name of the file to store tasks
        """
        self.filename = filename
        self.tasks = []
        self.load_tasks()  # Load existing tasks on startup
    
    # FUNCTIONALITY 1: ADD TASKS
    def add_task(self, task_description, priority='normal'):
        """
        Add a new task to the list and save to file.
        
        Args:
            task_description (str): Description of the task
            priority (str): Priority level ('low', 'normal', 'high')
        
        Returns:
            bool: True if task added successfully
        """
        if not task_description.strip():
            print("Error: Task description cannot be empty!")
            return False
        
        # Create task dictionary
        task = {
            'id': len(self.tasks) + 1,
            'description': task_description.strip(),
            'priority': priority,
            'status': 'pending'
        }
        
        self.tasks.append(task)
        self.save_tasks()
        print(f"✓ Task added: '{task_description}' (Priority: {priority})")
        return True
    
    # FUNCTIONALITY 2: REMOVE TASKS
    def remove_task(self, task_id):
        """
        Remove a task from the list by task ID and save to file.
        
        Args:
            task_id (int): ID of the task to remove
        
        Returns:
            bool: True if task removed successfully, False otherwise
        """
        for i, task in enumerate(self.tasks):
            if task['id'] == task_id:
                removed_task = self.tasks.pop(i)
                # Reassign IDs after removal to maintain consistency
                for j, t in enumerate(self.tasks):
                    t['id'] = j + 1
                self.save_tasks()
                print(f"✓ Task removed: '{removed_task['description']}'")
                return True
        
        print(f"Error: Task with ID {task_id} not found!")
        return False
    
    # FUNCTIONALITY 3: VIEW TASKS
    def view_tasks(self):
        """
        Display all tasks in a formatted way.
        """
        if not self.tasks:
            print("\nNo tasks available. Your task list is empty!")
            return
        
        print("\n" + "="*70)
        print("TASK LIST".center(70))
        print("="*70)
        
        for task in self.tasks:
            status_icon = "✓" if task['status'] == 'completed' else "○"
            priority_emoji = "🔴" if task['priority'] == 'high' else "🟡" if task['priority'] == 'normal' else "🟢"
            
            print(f"{status_icon} [{task['id']}] {priority_emoji} {task['description']}")
            print(f"    Status: {task['status']} | Priority: {task['priority']}")
        
        print("="*70 + "\n")
    
    # FUNCTIONALITY 4: MARK TASK AS COMPLETED
    def mark_completed(self, task_id):
        """
        Mark a task as completed.
        
        Args:
            task_id (int): ID of the task to mark as completed
        
        Returns:
            bool: True if successful, False otherwise
        """
        for task in self.tasks:
            if task['id'] == task_id:
                task['status'] = 'completed'
                self.save_tasks()
                print(f"✓ Task marked as completed: '{task['description']}'")
                return True
        
        print(f"Error: Task with ID {task_id} not found!")
        return False
    
    # DATA PERSISTENCE: SAVE TASKS
    def save_tasks(self):
        """
        Save all tasks to a text file in a formatted way.
        
        File Format:
        ID | Description | Priority | Status
        """
        try:
            with open(self.filename, 'w') as file:
                file.write("# Task Management System - Persistent Storage\n")
                file.write("# Format: ID | Description | Priority | Status\n")
                file.write("# " + "="*60 + "\n\n")
                
                if self.tasks:
                    for task in self.tasks:
                        # Format: ID|Description|Priority|Status
                        line = f"{task['id']}|{task['description']}|{task['priority']}|{task['status']}\n"
                        file.write(line)
                else:
                    file.write("# No tasks in the list\n")
            
            print(f"[System] Tasks saved to '{self.filename}'")
        
        except IOError as e:
            print(f"Error saving tasks: {e}")
    
    # DATA PERSISTENCE: LOAD TASKS
    def load_tasks(self):
        """
        Load tasks from the text file if it exists.
        
        Returns:
            bool: True if tasks loaded successfully
        """
        try:
            with open(self.filename, 'r') as file:
                lines = file.readlines()
            
            self.tasks = []
            for line in lines:
                line = line.strip()
                # Skip comments and empty lines
                if not line or line.startswith('#'):
                    continue
                
                # Parse line: ID|Description|Priority|Status
                parts = line.split('|')
                if len(parts) == 4:
                    task = {
                        'id': int(parts[0]),
                        'description': parts[1],
                        'priority': parts[2],
                        'status': parts[3]
                    }
                    self.tasks.append(task)
            
            if self.tasks:
                print(f"[System] Loaded {len(self.tasks)} tasks from '{self.filename}'")
            
            return True
        
        except FileNotFoundError:
            print(f"[System] New task file will be created: '{self.filename}'")
            self.tasks = []
            return False
        except Exception as e:
            print(f"Error loading tasks: {e}")
            self.tasks = []
            return False


# Demonstration of the Task Management System
print("="*70)
print("TASK MANAGEMENT SYSTEM - DEMONSTRATION".center(70))
print("="*70 + "\n")

# Create a TaskManager instance
tm = TaskManager('tasks.txt')

print("\n--- Adding Tasks ---")
tm.add_task("Complete Python assignment", "high")
tm.add_task("Review machine learning notes", "normal")
tm.add_task("Buy groceries", "low")
tm.add_task("Prepare for presentation", "high")
tm.add_task("Fix bug in project code", "normal")

print("\n--- Viewing All Tasks ---")
tm.view_tasks()

print("--- Marking Task 2 as Completed ---")
tm.mark_completed(2)

print("\n--- Updated Task List ---")
tm.view_tasks()

print("--- Removing Task 3 ---")
tm.remove_task(3)

print("\n--- Final Task List ---")
tm.view_tasks()

print("\nSystem completed successfully!")
# Display the contents of the tasks.txt file to show the file format
print("="*70)
print("FILE FORMAT - tasks.txt".center(70))
print("="*70)

with open('tasks.txt', 'r') as f:
    content = f.read()
    print(content)

print("\n" + "="*70)
print("FILE STRUCTURE EXPLANATION".center(70))
print("="*70)

file_structure = """
FORMAT BREAKDOWN:
─────────────────────────────────────────────────────────────

1. HEADER LINES (Comments for documentation):
   # Task Management System - Persistent Storage
   # Format: ID | Description | Priority | Status
   # ============================================================
   
   → These lines start with '#' and are used for documentation
   → They are skipped during data loading

2. DATA LINES (Task entries):
   ID|Description|Priority|Status
   
   Example:
   1|Complete Python assignment|high|pending
   2|Review machine learning notes|normal|completed
   3|Prepare for presentation|high|pending
   
   Parts:
   • ID: Unique task identifier (integer)
   • Description: Task description (string)
   • Priority: Priority level (low, normal, high)
   • Status: Task status (pending, completed)
   
   → Pipe character (|) acts as delimiter
   → Each task on a separate line
   → Simple, human-readable format

ADVANTAGES:
✓ Plain text format (readable in any text editor)
✓ Easy to parse and modify
✓ Portable across systems
✓ No external dependencies needed
✓ Simple backup and version control
"""

