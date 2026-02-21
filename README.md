# Banking-System
#include <iostream>
#include <vector>
#include <iomanip>
#include <string>

using namespace std;

// ================= TRANSACTION CLASS =================
class Transaction
{
public:
    int serial;
    string type;
    float amount;

    Transaction(int s, string t, float a)
    {
        serial = s;
        type = t;
        amount = a;
    }
};

// ================= ACCOUNT CLASS =================
class Account
{
private:
    int accountNumber;
    float balance;
    vector<Transaction> transactions;
    int transactionCount = 0;

public:
    Account(int accNo, float initialBalance)
    {
        accountNumber = accNo;
        balance = initialBalance;
        transactionCount++;
        transactions.push_back(Transaction(transactionCount, "Initial Deposit", initialBalance));
    }

    int getAccountNumber()
    {
        return accountNumber;
    }

    float getBalance()
    {
        return balance;
    }

    void deposit(float amount)
    {
        balance += amount;
        transactionCount++;
        transactions.push_back(Transaction(transactionCount, "Deposit", amount));

        cout << "\nSTATUS : Deposit Successful\n";
        cout << "Updated Balance (Rs) : " << balance << endl;
    }

    void withdraw(float amount)
    {
        if(amount > balance)
        {
            cout << "\nSTATUS : Insufficient Balance\n";
        }
        else
        {
            balance -= amount;
            transactionCount++;
            transactions.push_back(Transaction(transactionCount, "Withdrawal", amount));

            cout << "\nSTATUS : Withdrawal Successful\n";
            cout << "Available Balance (Rs): " << balance << endl;
        }
    }

    void transfer(Account &receiver, float amount)
    {
        if(amount > balance)
        {
            cout << "\nSTATUS : Transfer Failed (Insufficient Balance)\n";
        }
        else
        {
            balance -= amount;
            receiver.balance += amount;

            transactionCount++;
            transactions.push_back(Transaction(transactionCount, "Transfer Sent", amount));

            receiver.transactionCount++;
            receiver.transactions.push_back(Transaction(receiver.transactionCount, "Transfer Received", amount));

            cout << "\nSTATUS : Transfer Successful\n";
            cout << "Your New Balance (Rs): " << balance << endl;
        }
    }

    void showAccountDetails(string name, int customerID)
    {
        cout << "\n============================================================\n";
        cout << "                  ACCOUNT INFORMATION\n";
        cout << "============================================================\n";

        cout << "+----------------------+----------------------+\n";
        cout << "| Customer Name        | " << setw(20) << left << name << "|\n";
        cout << "| Customer ID          | " << setw(20) << left << customerID << "|\n";
        cout << "| Account Number       | " << setw(20) << left << accountNumber << "|\n";
        cout << "| Current Balance (Rs) | " << setw(20) << left << balance << "|\n";
        cout << "+----------------------+----------------------+\n";
    }

    void showTransactions()
    {
        cout << "\n============================================================\n";
        cout << "                 TRANSACTION HISTORY\n";
        cout << "============================================================\n";

        cout << "+----+----------------------+-------------+\n";
        cout << "| No | Transaction Type     | Amount (Rs) |\n";
        cout << "+----+----------------------+-------------+\n";

        for(auto &t : transactions)
        {
            cout << "| " << setw(2) << left << t.serial
                 << " | " << setw(20) << left << t.type
                 << " | " << setw(11) << left << t.amount
                 << " |\n";
        }

        cout << "+----+----------------------+-------------+\n";
        cout << "Current Balance : Rs " << balance << endl;
    }
};

// ================= CUSTOMER CLASS =================
class Customer
{
private:
    string name;
    int customerID;
    Account account;

public:
    Customer(string n, int id, int accNo, float balance)
        : account(accNo, balance)
    {
        name = n;
        customerID = id;
    }

    int getCustomerID()
    {
        return customerID;
    }

    string getName()
    {
        return name;
    }

    Account& getAccount()
    {
        return account;
    }
};

// ================= MAIN FUNCTION =================
int main()
{
    vector<Customer> customers;
    int choice;

    cout << "============================================================\n";
    cout << "            NATIONAL BANK MANAGEMENT SYSTEM\n";
    cout << "============================================================\n";

    do
    {
        cout << "\n-------------------- MAIN MENU --------------------\n";
        cout << "| 1 | Create New Customer Account               |\n";
        cout << "| 2 | Deposit Amount                           |\n";
        cout << "| 3 | Withdraw Amount                          |\n";
        cout << "| 4 | Transfer Funds                           |\n";
        cout << "| 5 | View Account Details                     |\n";
        cout << "| 6 | View Transaction History                 |\n";
        cout << "| 7 | Exit                                     |\n";
        cout << "-------------------------------------------------\n";

        cout << "Enter your choice: ";
        cin >> choice;

        if(choice == 1)
        {
            string name;
            int id, accNo;
            float balance;

            cout << "\nCREATE NEW CUSTOMER ACCOUNT\n";
            cout << "Customer Name        : ";
            cin >> name;
            cout << "Customer ID          : ";
            cin >> id;
            cout << "Account Number       : ";
            cin >> accNo;
            cout << "Initial Balance (Rs) : ";
            cin >> balance;

            customers.push_back(Customer(name, id, accNo, balance));

            cout << "\nSTATUS : Account successfully created.\n";
        }

        else if(choice == 2)
        {
            int id;
            float amount;

            cout << "\nEnter Customer ID: ";
            cin >> id;

            for(auto &c : customers)
            {
                if(c.getCustomerID() == id)
                {
                    cout << "Deposit Amount (Rs): ";
                    cin >> amount;
                    c.getAccount().deposit(amount);
                }
            }
        }

        else if(choice == 3)
        {
            int id;
            float amount;

            cout << "\nEnter Customer ID: ";
            cin >> id;

            for(auto &c : customers)
            {
                if(c.getCustomerID() == id)
                {
                    cout << "Withdrawal Amount (Rs): ";
                    cin >> amount;
                    c.getAccount().withdraw(amount);
                }
            }
        }

        else if(choice == 4)
        {
            int senderID, receiverID;
            float amount;

            cout << "Sender Customer ID   : ";
            cin >> senderID;
            cout << "Receiver Customer ID : ";
            cin >> receiverID;
            cout << "Transfer Amount (Rs) : ";
            cin >> amount;

            Account *sender = nullptr;
            Account *receiver = nullptr;

            for(auto &c : customers)
            {
                if(c.getCustomerID() == senderID)
                    sender = &c.getAccount();

                if(c.getCustomerID() == receiverID)
                    receiver = &c.getAccount();
            }

            if(sender && receiver)
                sender->transfer(*receiver, amount);
            else
                cout << "STATUS : Customer not found.\n";
        }

        else if(choice == 5)
        {
            int id;
            cout << "Enter Customer ID: ";
            cin >> id;

            for(auto &c : customers)
            {
                if(c.getCustomerID() == id)
                    c.getAccount().showAccountDetails(c.getName(), id);
            }
        }

        else if(choice == 6)
        {
            int id;
            cout << "Enter Customer ID: ";
            cin >> id;

            for(auto &c : customers)
            {
                if(c.getCustomerID() == id)
                    c.getAccount().showTransactions();
            }
        }

    } while(choice != 7);

    cout << "\nTHANK YOU FOR USING NATIONAL BANK SYSTEM\n";

    return 0;
}
