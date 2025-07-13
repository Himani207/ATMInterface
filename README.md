import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

public class ATMSystem {

    static class Transaction {
        String type;
        double amount;

        public Transaction(String type, double amount) {
            this.type = type;
            this.amount = amount;
        }

        @Override
        public String toString() {
            return type + ": $" + amount;
        }
    }

    static class BankAccount {
        private double balance;
        private List<Transaction> transactions;

        public BankAccount(double initialBalance) {
            this.balance = initialBalance;
            this.transactions = new ArrayList<>();
        }

        public double getBalance() {
            return balance;
        }

        public void deposit(double amount) {
            if (amount <= 0) {
                System.out.println("Deposit amount must be positive.");
                return;
            }
            balance += amount;
            transactions.add(new Transaction("Deposit", amount));
            System.out.println("Deposited $" + amount);
        }

        public boolean withdraw(double amount) {
            if (amount <= 0) {
                System.out.println("Withdraw amount must be positive.");
                return false;
            }
            if (balance >= amount) {
                balance -= amount;
                transactions.add(new Transaction("Withdraw", amount));
                System.out.println("Withdrew $" + amount);
                return true;
            } else {
                System.out.println("Insufficient balance.");
                return false;
            }
        }

        public boolean transfer(BankAccount toAccount, double amount) {
            if (amount <= 0) {
                System.out.println("Transfer amount must be positive.");
                return false;
            }
            if (balance >= amount) {
                balance -= amount;
                toAccount.balance += amount;
                transactions.add(new Transaction("Transfer to account", amount));
                toAccount.transactions.add(new Transaction("Transfer from account", amount));
                System.out.println("Transferred $" + amount);
                return true;
            } else {
                System.out.println("Insufficient balance to transfer.");
                return false;
            }
        }

        public void printTransactionHistory() {
            if (transactions.isEmpty()) {
                System.out.println("No transactions yet.");
                return;
            }
            System.out.println("Transaction History:");
            for (Transaction t : transactions) {
                System.out.println(t);
            }
        }
    }

    static class User {
        String userId;
        String pin;
        BankAccount account;

        public User(String userId, String pin, double initialBalance) {
            this.userId = userId;
            this.pin = pin;
            this.account = new BankAccount(initialBalance);
        }
    }

    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);

        // Setup demo users
        User user1 = new User("user1", "1234", 1000);
        User user2 = new User("user2", "5678", 500);

        User currentUser = null;

        System.out.println("Welcome to the Java ATM!");

        // Login loop
        while (true) {
            System.out.print("Enter User ID: ");
            String inputUserId = scanner.nextLine();

            System.out.print("Enter PIN: ");
            String inputPin = scanner.nextLine();

            if (inputUserId.equals(user1.userId) && inputPin.equals(user1.pin)) {
                currentUser = user1;
                break;
            } else if (inputUserId.equals(user2.userId) && inputPin.equals(user2.pin)) {
                currentUser = user2;
                break;
            } else {
                System.out.println("Invalid User ID or PIN. Try again.");
            }
        }

        System.out.println("Login successful! Welcome " + currentUser.userId);

        // ATM menu loop
        while (true) {
            System.out.println("\nATM Menu:");
            System.out.println("1. Transaction History");
            System.out.println("2. Withdraw");
            System.out.println("3. Deposit");
            System.out.println("4. Transfer");
            System.out.println("5. Quit");
            System.out.print("Choose an option: ");

            String choice = scanner.nextLine();

            switch (choice) {
                case "1":
                    currentUser.account.printTransactionHistory();
                    break;

                case "2":
                    System.out.print("Enter amount to withdraw: ");
                    double withdrawAmount = Double.parseDouble(scanner.nextLine());
                    currentUser.account.withdraw(withdrawAmount);
                    break;

                case "3":
                    System.out.print("Enter amount to deposit: ");
                    double depositAmount = Double.parseDouble(scanner.nextLine());
                    currentUser.account.deposit(depositAmount);
                    break;

                case "4":
                    System.out.print("Enter recipient User ID: ");
                    String recipientId = scanner.nextLine();
                    User recipientUser = null;
                    if (recipientId.equals(user1.userId)) {
                        recipientUser = user1;
                    } else if (recipientId.equals(user2.userId)) {
                        recipientUser = user2;
                    }
                    if (recipientUser == null) {
                        System.out.println("Recipient user not found.");
                        break;
                    }
                    if (recipientUser == currentUser) {
                        System.out.println("Cannot transfer to yourself.");
                        break;
                    }
                    System.out.print("Enter amount to transfer: ");
                    double transferAmount = Double.parseDouble(scanner.nextLine());
                    currentUser.account.transfer(recipientUser.account, transferAmount);
                    break;

                case "5":
                    System.out.println("Thank you for using the ATM. Goodbye!");
                    scanner.close();
                    return;

                default:
                    System.out.println("Invalid option, try again.");
            }
            System.out.printf("Current balance: $%.2f\n", currentUser.account.getBalance());
        }
    }
}
# ATMInterface
