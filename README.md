# 2300062---SD-Assgmt
# Analysis:

# 1. SOLID Principles
Single Responsibility Principle (SRP):
The StudentDatabase class is doing too many things. It handles storing students, adding, removing, updating them, and even searching. This makes the class bulky and harder to manage.
If you need to change how students are updated or add a new feature, you might end up tweaking the class in several places, which can introduce bugs.


# Interface Segregation Principle (ISP):
 If you have an interface with many methods, classes that don’t need all those methods are forced to implement them. This can make your code harder to work with.
If a class only needs to manage students in memory, it shouldn’t be forced to handle file operations.

# Dependency Inversion Principle (DIP):
The StudentManagementSystem directly depends on the StudentDatabase. This means any changes in StudentDatabase could potentially impact StudentManagementSystem.
If StudentDatabase changes its internal workings, you might need to update StudentManagementSystem as well.

# 2. DRY (Don’t Repeat Yourself)
The code for searching students by ID, name, or major is quite similar and repeated in multiple places. This makes the code harder to maintain and more prone to errors.
The logic for checking each student’s details is duplicated across different methods.

# 3. KISS (Keep It Simple Stupid)
The menu system is a bit complex and could be simplified. Having many separate methods for different operations makes the system more complicated than necessary.
Instead of having lots of individual methods for each menu option, consolidating related operations could make the code simpler and easier to follow.

# 4. YAGNI (You Ain’t Gonna Need It)
Some features, like saving and loading data from files, might not be necessary if the system is only meant for quick, in-memory operations.
If the system is only used for a single session and doesn’t need to remember data between runs, file operations might be overkill.


# Refactoring:
Refactored Code

""""
PYTHON
""""
# Interface for Student operations
class StudentOperations(ABC):
    @abstractmethod
    def add_student(self, student_id, name, age, major):
        pass

    @abstractmethod
    def remove_student(self, student_id):
        pass

    @abstractmethod
    def update_student(self, student_id, name=None, age=None, major=None):
        pass

    @abstractmethod
    def display_all_students(self):
        pass


# Student class
class Student:
    def __init__(self, student_id, name, age, major):
        """
        Initialize a new Student object.
        """
        self.student_id = student_id
        self.name = name
        self.age = age
        self.major = major

    def update(self, name=None, age=None, major=None):
        """
        Update student information.
        """
        if name is not None:
            self.name = name
        if age is not None and isinstance(age, int) and age > 0:
            self.age = age
        if major is not None:
            self.major = major

    def display(self):
        """
        Display student information.
        """
        print(f"ID: {self.student_id}, Name: {self.name}, Age: {self.age}, Major: {self.major}")


# StudentDatabase class
class StudentDatabase(StudentOperations):
    def __init__(self):
        """
        Initialize the student database.
        """
        self.students = {}

    def add_student(self, student_id, name, age, major):
        if student_id in self.students:
            print("Student with this ID already exists.")
            return
        self.students[student_id] = Student(student_id, name, age, major)

    def remove_student(self, student_id):
        if student_id in self.students:
            del self.students[student_id]
        else:
            print("Student ID not found.")

    def update_student(self, student_id, name=None, age=None, major=None):
        student = self.students.get(student_id)
        if student:
            student.update(name, age, major)
        else:
            print("Student ID not found.")

    def display_all_students(self):
        if not self.students:
            print("No students in the database.")
        for student in self.students.values():
            student.display()


# StudentManagementSystem class
class StudentManagementSystem:
    def __init__(self, database):
        """
        Initialize the student management system with a given database.
        """
        self.database = database

    def show_menu(self):
        """
        Display the menu and handle user input.
        """
        while True:
            print("\nStudent Management System Menu")
            print("1. Add New Student")
            print("2. Remove Student")
            print("3. Update Student Info")
            print("4. Show All Students")
            print("5. Exit")

            choice = input("Enter your choice: ")

            if choice == '1':
                self.add_new_student()
            elif choice == '2':
                self.delete_student()
            elif choice == '3':
                self.update_student_info()
            elif choice == '4':
                self.database.display_all_students()
            elif choice == '5':
                break
            else:
                print("Invalid choice. Please try again.")

    def add_new_student(self):
        """
        Handle the addition of a new student.
        """
        student_id = int(input("Enter student ID: "))
        name = input("Enter student name: ")
        age = int(input("Enter student age: "))
        major = input("Enter student major: ")
        self.database.add_student(student_id, name, age, major)

    def delete_student(self):
        """
        Handle the removal of a student.
        """
        student_id = int(input("Enter student ID to remove: "))
        self.database.remove_student(student_id)

    def update_student_info(self):
        """
        Handle updating a student's information.
        """
        student_id = int(input("Enter student ID to update: "))
        name = input("Enter new name (leave blank if no change): ")
        age_str = input("Enter new age (leave blank if no change): ")
        major = input("Enter new major (leave blank if no change): ")

        age = int(age_str) if age_str else None

        self.database.update_student(student_id, name if name else None, age, major if major else None)


# Example Usage
if __name__ == "__main__":
    database = StudentDatabase()
    system = StudentManagementSystem(database)
    system.show_menu()

# Conclusion
The process of reviewing, refactoring, and documenting the Student Management System has been instrumental in transforming it into a more robust and maintainable application. The abpve documentation is a litmus enough          
