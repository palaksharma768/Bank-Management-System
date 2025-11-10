# Bank-Management-System
#include <iostream>
#include <string>
using namespace std;

// Base Class
class BankAccount {
protected:
    string name;
    int accNumber;
    double balance;

public:
    BankAccount(string n, int acc, double bal) {
        name = n;
        accNumber = acc;
        balance = bal;
    }

    virtual void deposit(double amount) {
        balance += amount;
        cout << "Deposited: " << amount << " | New Balance: " << balance << endl;
    }

    virtual void withdraw(double amount) {
        if (amount > balance)
            cout << "Insufficient Balance!\n";
        else {
            balance -= amount;
            cout << "Withdrawn: " << amount << " | New Balance: " << balance << endl;
        }
    }

    virtual void display() const {
        cout << "\nAccount Holder: " << name;
        cout << "\nAccount Number: " << accNumber;
        cout << "\nBalance       : " << balance << endl;
    }

    virtual ~BankAccount() {} // virtual destructor
};

// Derived Class 1 - Savings Account
class SavingsAccount : public BankAccount {
    double interestRate;

public:
    SavingsAccount(string n, int acc, double bal, double rate)
        : BankAccount(n, acc, bal), interestRate(rate) {}

    void addInterest() {
        double interest = balance * interestRate / 100;
        balance += interest;
        cout << "Interest added: " << interest << " | New Balance: " << balance << endl;
    }

    void display() const override {
        cout << "\n--- Savings Account ---";
        BankAccount::display();
        cout << "Interest Rate  : " << interestRate << "%\n";
    }
};

// Derived Class 2 - Current Account
class CurrentAccount : public BankAccount {
    double overdraftLimit;

public:
    CurrentAccount(string n, int acc, double bal, double limit)
        : BankAccount(n, acc, bal), overdraftLimit(limit) {}

    void withdraw(double amount) override {
        if (amount > balance + overdraftLimit)
            cout << "Withdrawal exceeds overdraft limit!\n";
        else {
            balance -= amount;
            cout << "Withdrawn: " << amount << " | New Balance: " << balance << endl;
        }
    }

    void display() const override {
        cout << "\n--- Current Account ---";
        BankAccount::display();
        cout << "Overdraft Limit: " << overdraftLimit << endl;
    }
};

// Main Function
int main() {
    int choice;
    BankAccount *account = nullptr;

    cout << "==== Bank Account Management ====\n";
    cout << "1. Create Savings Account\n";
    cout << "2. Create Current Account\n";
    cout << "Enter your choice: ";
    cin >> choice;

    string name;
    int accNo;
    double bal;

    cout << "Enter Account Holder Name: ";
    cin.ignore();
    getline(cin, name);
    cout << "Enter Account Number: ";
    cin >> accNo;
    cout << "Enter Initial Balance: ";
    cin >> bal;

    if (choice == 1) {
        double rate;
        cout << "Enter Interest Rate (%): ";
        cin >> rate;
        account = new SavingsAccount(name, accNo, bal, rate);
    } else if (choice == 2) {
        double limit;
        cout << "Enter Overdraft Limit: ";
        cin >> limit;
        account = new CurrentAccount(name, accNo, bal, limit);
    } else {
        cout << "Invalid choice!\n";
        return 0;
    }

    int option;
    do {
        cout << "\n--- Menu ---\n";
        cout << "1. Deposit Money\n";
        cout << "2. Withdraw Money\n";
        cout << "3. Display Details\n";
        if (choice == 1) cout << "4. Add Interest\n";
        cout << "5. Exit\n";
        cout << "Enter option: ";
        cin >> option;

        double amt;
        switch (option) {
        case 1:
            cout << "Enter amount to deposit: ";
            cin >> amt;
            account->deposit(amt);
            break;
        case 2:
            cout << "Enter amount to withdraw: ";
            cin >> amt;
            account->withdraw(amt);
            break;
        case 3:
            account->display();
            break;
        case 4:
            if (choice == 1) {
                SavingsAccount *sa = dynamic_cast<SavingsAccount *>(account);
                if (sa) sa->addInterest();
            } else {
                cout << "Interest feature not available for Current Account.\n";
            }
            break;
        case 5:
            cout << "Exiting program...\n";
            break;
        default:
            cout << "Invalid option!\n";
        }
    } while (option != 5);

    delete account;
    return 0;
}
