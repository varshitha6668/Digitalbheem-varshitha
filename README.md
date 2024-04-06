import java.util.*;

class Transaction {
    private String type;
    private double amount;
    private Date timestamp;

    public Transaction(String type, double amount) {
        this.type = type;
        this.amount = amount;
        this.timestamp = new Date();
    }

    public String getType() {
        return type;
    }

    public double getAmount() {
        return amount;
    }

    public Date getTimestamp() {
        return timestamp;
    }
}

class Account {
    private String userId;
    private String pin;
    private double balance;
    private List<Transaction> transactions;

    public Account(String userId, String pin) {
        this.userId = userId;
        this.pin = pin;
        this.balance = 0.0;
        this.transactions = new ArrayList<>();
    }

    public String getUserId() {
        return userId;
    }

    public String getPin() {
        return pin;
    }

    public void deposit(double amount) {
        balance += amount;
        transactions.add(new Transaction("Deposit", amount));
        System.out.println("Deposit successful. Current balance: " + balance);
    }

    public void withdraw(double amount) {
        if (amount > balance) {
            System.out.println("Insufficient balance.");
        } else {
            balance -= amount;
            transactions.add(new Transaction("Withdrawal", amount));
            System.out.println("Withdrawal successful. Current balance: " + balance);
        }
    }

    public void transfer(Account recipient, double amount) {
        if (amount > balance) {
            System.out.println("Insufficient balance.");
        } else {
            balance -= amount;
            recipient.balance += amount;
            transactions.add(new Transaction("Transfer to " + recipient.getUserId(), amount));
            System.out.println("Transfer successful. Current balance: " + balance);
        }
    }

    public void transactionHistory() {
        System.out.println("Transaction History:");
        for (Transaction transaction : transactions) {
            System.out.println(transaction.getType() + " - Amount: " + transaction.getAmount() + " - Timestamp: " + transaction.getTimestamp());
        }
    }
}

class ATM {
    private List<Account> accounts;
    private Account currentAccount;

    public ATM() {
        this.accounts = new ArrayList<>();
    }

    public List<Account> getAccounts() {
        return accounts;
    }

    public Account getCurrentAccount() {
        return currentAccount;
    }

    public void login(String userId, String pin) {
        for (Account account : accounts) {
            if (account.getUserId().equals(userId) && account.getPin().equals(pin)) {
                currentAccount = account;
                System.out.println("Login successful. Welcome, " + userId + "!");
                return;
            }
        }
        System.out.println("Invalid user ID or PIN.");
    }

    public void quit() {
        System.out.println("Thank you for using the ATM. Goodbye!");
        System.exit(0);
    }
}

public class Main {
    public static void main(String[] args) {
        ATM atm = new ATM();
        // Assuming some accounts are created
        atm.getAccounts().add(new Account("user1", "1234"));
        atm.getAccounts().add(new Account("user2", "5678"));

        Scanner scanner = new Scanner(System.in);

        System.out.print("Enter user ID: ");
        String userId = scanner.nextLine();

        System.out.print("Enter PIN: ");
        String pin = scanner.nextLine();

        atm.login(userId, pin);

        if (atm.getCurrentAccount() != null) {
            while (true) {
                System.out.println("\nChoose an option:");
                System.out.println("1. Deposit");
                System.out.println("2. Withdraw");
                System.out.println("3. Transfer");
                System.out.println("4. Transaction History");
                System.out.println("5. Quit");

                System.out.print("Enter your choice: ");
                int choice = scanner.nextInt();

                switch (choice) {
                    case 1:
                        System.out.print("Enter deposit amount: ");
                        double depositAmount = scanner.nextDouble();
                        atm.getCurrentAccount().deposit(depositAmount);
                        break;
                    case 2:
                        System.out.print("Enter withdrawal amount: ");
                        double withdrawalAmount = scanner.nextDouble();
                        atm.getCurrentAccount().withdraw(withdrawalAmount);
                        break;
                    case 3:
                        System.out.print("Enter recipient's user ID: ");
                        String recipientId = scanner.next();
                        System.out.print("Enter transfer amount: ");
                        double transferAmount = scanner.nextDouble();
                        Account recipient = null;
                        for (Account account : atm.getAccounts()) {
                            if (account.getUserId().equals(recipientId)) {
                                recipient = account;
                                break;
                            }
                        }
                        if (recipient != null) {
                            atm.getCurrentAccount().transfer(recipient, transferAmount);
                        } else {
                            System.out.println("Recipient not found.");
                        }
                        break;
                    case 4:
                        atm.getCurrentAccount().transactionHistory();
                        break;
                    case 5:
                        atm.quit();
                        break;
                    default:
                        System.out.println("Invalid choice. Please enter a number from 1 to 5.");
                }
            }
        }
    }
}
