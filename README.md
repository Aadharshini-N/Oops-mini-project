# Oops-mini-project

ONLINE EXAM PORTAL
PO–PSO Mapping Table
PO1 | PO2 | PO3 | PO4 | PO5 | PO6 | PO7 | PO8 | PO9 | PO10 | PO11 | PO12 | PSO1 | PSO2 | PSO3
3 | 2 | 3 | 2 | 2 | 1 | - | 3 | 1 | 2 | - | - | 3 | 2 | 1
PSO Justification

PSO1 – Apply fundamental computing knowledge (3): Applies core Java programming concepts, OOP principles, and file/database handling to develop a secure online exam system.
PSO2 – Design and implement solutions (2): Designs and develops modular components such as student registration, authentication, exam management, and result generation.
PSO3 – Use modern tools and technologies (1): Utilizes JavaFX/Swing for GUI, JDBC for database interaction, and Git-based deployment tools for version control.

PO Justification

PO1 – Engineering Knowledge (3): Applies computing fundamentals, Java programming, and database concepts to design a secure and efficient online exam application.
PO2 – Problem Analysis (2): Identifies and resolves issues such as invalid authentication, exam submission errors, and unauthorized access.
PO3 – Design/Development of Solutions (3): Designs a complete application integrating GUI, backend logic, and secure data storage mechanisms.
PO4 – Conduct Investigations (2): Analyzes question management, evaluation, and backend validation to maintain accuracy and fairness.
PO5 – Modern Tool Usage (2): Employs tools like NetBeans/Eclipse IDE, MySQL, and JavaFX for implementation.
PO6 – The Engineer and Society (1): Promotes digital transformation in education through online examinations.
PO8 – Ethics (3): Ensures ethical software practices with secure authentication and data privacy.
PO9 – Individual and Team Work (1): Encourages collaboration during system development and testing.
PO10 – Communication (2): Demonstrates effective communication through structured design and documentation.

Introduction

The Online Exam Portal project provides a secure and user-friendly platform for conducting online tests. It enables students to register, log in, take exams, and view results instantly. 
Developed using Java, it integrates frontend (JavaFX) and backend (JDBC–MySQL) modules implementing OOP, file handling, and GUI principles.

Project Distribution

Module 1 – User Registration: Handles student registration and database storage.
Module 2 – Login & Authentication: Verifies user credentials and provides secure access.
Module 3 – Exam Creation & Management: Allows admin to manage exams and questions.
Module 4 – Exam Attempt & Submission: Enables students to attempt and submit exams securely.
Module 5 – Result Generation: Evaluates and displays performance reports.



System Architecture

 
Coding

Module 1:
package aaa;

import java.util.ArrayList;

abstract class User {
    protected String username;
    protected String password;

    public User(String username, String password) {
        this.username = username;
        this.password = password;
    }

    public abstract void displayInfo();
}

class Student extends User {
    private int studentId;

    public Student(String username, String password, int studentId) {
        super(username, password);
        this.studentId = studentId;
    }

    @Override
    public void displayInfo() {
        System.out.println("Student: " + username + " (ID: " + studentId + ")");
    }
}

class Question {
    private String questionText;
    private String[] options;
    private int correctAnswer;

    public Question(String questionText, String[] options, int correctAnswer) {
        this.questionText = questionText;
        this.options = options;
        this.correctAnswer = correctAnswer;
    }

    public boolean checkAnswer(int answer) {
        return answer == correctAnswer;
    }

    public void display() {
        System.out.println(questionText);
        for (int i = 0; i < options.length; i++) {
            System.out.println((i + 1) + ". " + options[i]);
        }
    }
}

class Exam {
    private ArrayList<Question> questions = new ArrayList<>();

    public void addQuestion(Question q) {
        questions.add(q);
    }

    public ArrayList<Question> getQuestions() {
        return questions;
    }
}

Module 2:
package aaa;

import java.io.*;

public class Module2 {

    public static void saveText(String filename, String text) {
        try (FileWriter fw = new FileWriter(filename, true)) {
            fw.write(text + "\n");
            System.out.println("✅ Data saved to " + filename);
        } catch (IOException e) {
            System.err.println("❌ Error writing to file: " + e.getMessage());
        }
    }

    public static void readText(String filename) {
        try (BufferedReader br = new BufferedReader(new FileReader(filename))) {
            System.out.println("📖 File content:");
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
        } catch (FileNotFoundException e) {
            System.err.println("⚠️ File not found!");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        saveText("questions.txt", "What is Java?;1) Language;2) OS;3) Browser;4) None;1");
        readText("questions.txt");
    }
}
Module 3:
package aaa;

import java.util.List;

public class Module3<T extends User> extends Thread {
    private T participant;
    private Exam exam;

    public Module3(T participant, Exam exam) {
        this.participant = participant;
        this.exam = exam;
    }

    @Override
    public void run() {
        System.out.println("🧑 " + participant.username + " started the exam...");
        List<Question> questions = exam.getQuestions();
        for (Question q : questions) {
            q.display();
            q.checkAnswer(1); // Simulated answer
        }
        System.out.println("✅ " + participant.username + " completed the exam!");
    }

    public static void main(String[] args) {
        // Create exam and add questions
        Exam exam = new Exam();
        exam.addQuestion(new Question("Java is:", new String[]{"Language", "OS"}, 1));

        // Create students
        Student s1 = new Student("Aadhu", "pass", 101);
        Student s2 = new Student("Ram", "pass", 102);

        // Create threads for each student
        Module3<Student> t1 = new Module3<>(s1, exam);
        Module3<Student> t2 = new Module3<>(s2, exam);

        // Start both threads
        t1.start();
        t2.start();
    }
}

Module 4:
package aaa;

import java.rmi.*;
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;
import java.rmi.server.UnicastRemoteObject;
import java.sql.*;

// RMI Server + MySQL integration
public class Module4 extends UnicastRemoteObject implements ExamRemote {
    private static final long serialVersionUID = 1L;

    protected Module4() throws RemoteException {
        super();
    }

    @Override
    public String saveResult(String username, int score) throws RemoteException {
        try (Connection con = DriverManager.getConnection(
                "jdbc:mysql://localhost:3306/ExamDB?useSSL=false&serverTimezone=UTC",
                "root", "Aadhu2006");
             PreparedStatement ps = con.prepareStatement(
                     "INSERT INTO results (username, score) VALUES (?, ?)")) {

            ps.setString(1, username);
            ps.setInt(2, score);
            ps.executeUpdate();
            return "✅ Result saved for " + username;

        } catch (SQLException e) {
            e.printStackTrace();
            return "❌ Database error: " + e.getMessage();
        }
    }

    public static void main(String[] args) {
        try {
            // ✅ Try connecting to an existing registry
            Registry registry;
            try {
                registry = LocateRegistry.getRegistry(1099);
                registry.list(); // test if it’s alive
                System.out.println("⚠️ RMI registry already running on port 1099");
            } catch (RemoteException e) {
                System.out.println("🔄 No registry found — starting a new one on port 1099...");
                registry = LocateRegistry.createRegistry(1099);
                System.out.println("✅ RMI registry started on port 1099");
            }

            // Create server and bind it
            Module4 server = new Module4();
            Naming.rebind("rmi://localhost:1099/ExamService", server);
            System.out.println("🚀 ExamService bound and ready for clients...");

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}


package aaa;

import java.rmi.Naming;

public class Module4Client {
    public static void main(String[] args) {
        try {
            // Lookup the remote object from registry
            ExamRemote examService = (ExamRemote) Naming.lookup("rmi://localhost:1099/ExamService");

            // Call the remote method
            String response = examService.saveResult("Aadhu", 95);
            System.out.println(response);

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}


// ✅ Remote interface
interface ExamRemote extends Remote {
    String saveResult(String username, int score) throws RemoteException;
}

Module 5:
package aaa;

import javax.swing.*;
import java.awt.*;
import java.awt.event.*;
import java.rmi.Naming;

public class Module5 extends JFrame {
    private JTextField usernameField;
    private JButton submitButton;

    public Module5() {
        setTitle("Online Exam Portal");
        setLayout(new FlowLayout());

        add(new JLabel("Enter Username:"));
        usernameField = new JTextField(15);
        add(usernameField);

        submitButton = new JButton("Submit Result");
        add(submitButton);

        submitButton.addActionListener(new ActionListener() {
            public void actionPerformed(ActionEvent e) {
                try {
                    ExamRemote service = (ExamRemote) Naming.lookup("rmi://localhost/ExamService");
                    String result = service.saveResult(usernameField.getText(), 90);
                    JOptionPane.showMessageDialog(null, result);
                } catch (Exception ex) {
                    ex.printStackTrace();
                }
            }
        });

        setSize(300, 150);
        setDefaultCloseOperation(EXIT_ON_CLOSE);
        setVisible(true);
    }

    public static void main(String[] args) {
        new Module5();
    }
}



