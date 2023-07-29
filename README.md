# oibsip_taskno
package Intership;

import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;

class User {
    private String userId;
    private String userPin;
    private double balance;
    private StringBuilder transactionHistory;

    public User(String userId, String userPin, double initialBalance) {
        this.userId = userId;
        this.userPin = userPin;
        this.balance = initialBalance;
        this.transactionHistory = new StringBuilder();
    }

    public String getUserId() {
        return userId;
    }

    public boolean authenticate(String userPin) {
        return this.userPin.equals(userPin);
    }

    public double getBalance() {
        return balance;
    }

    public void deposit(double amount) {
        balance += amount;
        addTransactionToHistory("Deposit", amount);
    }

    public void withdraw(double amount) {
        if (amount <= balance) {
            balance -= amount;
            addTransactionToHistory("Withdraw", -amount);
        } else {
            System.out.println("Insufficient funds!");
        }
    }

    public void transfer(User receiver, double amount) {
        if (amount <= balance) {
            balance -= amount;
            receiver.deposit(amount);
            addTransactionToHistory("Transfer to " + receiver.getUserId(), -amount);
            receiver.addTransactionToHistory("Transfer from " + getUserId(), amount);
        } else {
            System.out.println("Insufficient funds!");
        }
    }

    public void addTransactionToHistory(String transactionType, double amount) {
        transactionHistory.append(transactionType).append(": ").append(amount).append("\n");
    }

    public String getTransactionHistory() {
        return transactionHistory.toString();
    }
}

public class ATM {
    private Map<String, User> users;
    private User currentUser;
    private Scanner scanner;

    public ATM() {
        this.users = new HashMap<>();
        this.scanner = new Scanner(System.in);
    }

    public void addUser(User user) {
        users.put(user.getUserId(), user);
    }

    public void start() {
        System.out.print("Enter your user ID: ");
        String userId = scanner.nextLine();
        System.out.print("Enter your user PIN: ");
        String userPin = scanner.nextLine();

        currentUser = users.get(userId);
        if (currentUser != null && currentUser.authenticate(userPin)) {
            showMainMenu();
        } else {
            System.out.println("Invalid user ID or PIN.");
        }
    }

    public void showMainMenu() {
        System.out.println("Welcome, " + currentUser.getUserId() + "!");
        int choice;
        do {
            System.out.println("\nMain Menu");
            System.out.println("1. View Transactions History");
            System.out.println("2. Deposit");
            System.out.println("3. Withdraw");
            System.out.println("4. Transfer");
            System.out.println("5. Quit");
            System.out.print("Enter your choice: ");
            choice = scanner.nextInt();
            scanner.nextLine(); // Consume the newline character

            switch (choice) {
                case 1:
                    viewTransactionsHistory();
                    break;
                case 2:
                    deposit();
                    break;
                case 3:
                    withdraw();
                    break;
                case 4:
                    transfer();
                    break;
                case 5:
                    quit();
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        } while (choice != 5);
    }

    public void viewTransactionsHistory() {
        System.out.println("\nTransaction History:");
        System.out.println(currentUser.getTransactionHistory());
    }

    public void deposit() {
        System.out.print("Enter the amount to deposit: ");
        double amount = scanner.nextDouble();
        scanner.nextLine(); // Consume the newline character
        currentUser.deposit(amount);
        System.out.println("Deposit successful. Current balance: " + currentUser.getBalance());
    }

    public void withdraw() {
        System.out.print("Enter the amount to withdraw: ");
        double amount = scanner.nextDouble();
        scanner.nextLine(); // Consume the newline character
        currentUser.withdraw(amount);
        System.out.println("Withdrawal successful. Current balance: " + currentUser.getBalance());
    }

    public void transfer() {
        System.out.print("Enter the recipient's user ID: ");
        String recipientId = scanner.nextLine();
        User recipient = users.get(recipientId);

        if (recipient != null) {
            System.out.print("Enter the amount to transfer: ");
            double amount = scanner.nextDouble();
            scanner.nextLine(); // Consume the newline character
            currentUser.transfer(recipient, amount);
            System.out.println("Transfer successful. Current balance: " + currentUser.getBalance());
        } else {
            System.out.println("Recipient user ID not found.");
        }
    }

    public void quit() {
        System.out.println("Thank you for using the ATM. Have a nice day!");
        scanner.close();
        System.exit(0);
    }

    public static void main(String[] args) {
        // Create users and initialize the ATM system
        User user1 = new User("user1", "1234", 1000);
        User user2 = new User("user2", "5678", 2000);

        ATM atm = new ATM();
        atm.addUser(user1);
        atm.addUser(user2);

        atm.start();
    }
}
