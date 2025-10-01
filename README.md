import java.io.*;
import java.util.*;
public class JavaExperiment{
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int choice = 0;

        while (true) {
            System.out.println("\n===== Java Experiment Menu =====");
            System.out.println("1. Sum of Integers (Autoboxing/Unboxing)");
            System.out.println("2. Serialize & Deserialize Student");
            System.out.println("3. Employee Management System");
            System.out.println("4. Exit");
            System.out.print("Enter your choice: ");
            choice = sc.nextInt();
            sc.nextLine(); // consume newline

            switch (choice) {
                case 1:
                    sumOfIntegers(sc);
                    break;
                case 2:
                    studentSerialization(sc);
                    break;
                case 3:
                    employeeManagement(sc);
                    break;
                case 4:
                    System.out.println("Exiting...");
                    sc.close();
                    System.exit(0);
                default:
                    System.out.println("Invalid choice!");
            }
        }
    }

    // Part A: Sum of integers using autoboxing/unboxing
    public static void sumOfIntegers(Scanner sc) {
        System.out.print("Enter number of integers: ");
        int n = sc.nextInt();
        sc.nextLine();
        List<Integer> numbers = new ArrayList<>();

        for (int i = 0; i < n; i++) {
            System.out.print("Enter integer #" + (i + 1) + ": ");
            String input = sc.nextLine();
            Integer num = Integer.parseInt(input); // Autoboxing
            numbers.add(num);
        }

        int sum = 0;
        for (Integer num : numbers) {
            sum += num; // Unboxing
        }

        System.out.println("Sum of integers: " + sum);
    }

    // Part B: Serialization/Deserialization of Student
    public static void studentSerialization(Scanner sc) {
        System.out.print("Enter student ID: ");
        int id = sc.nextInt();
        sc.nextLine();
        System.out.print("Enter student name: ");
        String name = sc.nextLine();
        System.out.print("Enter student grade: ");
        String grade = sc.nextLine();

        Student s = new Student(id, name, grade);

        try {
            // Serialize
            FileOutputStream fos = new FileOutputStream("student.dat");
            ObjectOutputStream oos = new ObjectOutputStream(fos);
            oos.writeObject(s);
            oos.close();
            fos.close();
            System.out.println("Student serialized successfully!");

            // Deserialize
            FileInputStream fis = new FileInputStream("student.dat");
            ObjectInputStream ois = new ObjectInputStream(fis);
            Student s2 = (Student) ois.readObject();
            ois.close();
            fis.close();

            System.out.println("Deserialized Student: " + s2);
        } catch (Exception e) {
            System.out.println("Error: " + e.getMessage());
        }
    }

    // Part C: Menu-driven Employee management
    public static void employeeManagement(Scanner sc) {
        String fileName = "employees.dat";
        while (true) {
            System.out.println("\n--- Employee Management Menu ---");
            System.out.println("1. Add Employee");
            System.out.println("2. Display All Employees");
            System.out.println("3. Exit to Main Menu");
            System.out.print("Enter your choice: ");
            int ch = sc.nextInt();
            sc.nextLine();

            switch (ch) {
                case 1:
                    try {
                        System.out.print("Enter Employee ID: ");
                        int id = sc.nextInt();
                        sc.nextLine();
                        System.out.print("Enter Employee Name: ");
                        String name = sc.nextLine();
                        System.out.print("Enter Designation: ");
                        String desig = sc.nextLine();
                        System.out.print("Enter Salary: ");
                        double salary = sc.nextDouble();
                        sc.nextLine();

                        Employee emp = new Employee(id, name, desig, salary);

                        FileOutputStream fos = new FileOutputStream(fileName, true);
                        ObjectOutputStream oos;
                        if (new File(fileName).length() == 0) {
                            oos = new ObjectOutputStream(fos);
                        } else {
                            oos = new AppendableObjectOutputStream(fos);
                        }

                        oos.writeObject(emp);
                        oos.close();
                        fos.close();
                        System.out.println("Employee added successfully!");
                    } catch (Exception e) {
                        System.out.println("Error: " + e.getMessage());
                    }
                    break;

                case 2:
                    try {
                        FileInputStream fis = new FileInputStream(fileName);
                        ObjectInputStream ois = new ObjectInputStream(fis);

                        System.out.println("\n--- Employee Records ---");
                        while (true) {
                            Employee emp = (Employee) ois.readObject();
                            System.out.println(emp);
                        }
                    } catch (EOFException e) {
                        // End of file reached
                    } catch (FileNotFoundException e) {
                        System.out.println("No employees found.");
                    } catch (Exception e) {
                        System.out.println("Error: " + e.getMessage());
                    }
                    break;

                case 3:
                    return;

                default:
                    System.out.println("Invalid choice!");
            }
        }
    }
}

// Student class
class Student implements Serializable {
    private static final long serialVersionUID = 1L;
    int studentID;
    String name;
    String grade;

    public Student(int id, String name, String grade) {
        this.studentID = id;
        this.name = name;
        this.grade = grade;
    }

    @Override
    public String toString() {
        return studentID + " - " + name + " - " + grade;
    }
}

// Employee class
class Employee implements Serializable {
    private static final long serialVersionUID = 1L;
    int empID;
    String name;
    String designation;
    double salary;

    public Employee(int empID, String name, String designation, double salary) {
        this.empID = empID;
        this.name = name;
        this.designation = designation;
        this.salary = salary;
    }

    @Override
    public String toString() {
        return empID + " - " + name + " - " + designation + " - " + salary;
    }
}

// AppendableObjectOutputStream to append objects
class AppendableObjectOutputStream extends ObjectOutputStream {
    public AppendableObjectOutputStream(OutputStream out) throws IOException {
        super(out);
    }

    @Override
    protected void writeStreamHeader() throws IOException {
        reset();
    }
}
