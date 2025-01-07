
# Refactored BankAccount Class

## Code Explanation

### Refactoring Steps:

1. **Encapsulation**: The `Balance`, `AccountNumber`, and `Owner` fields are now private and managed via public properties and methods.
2. **Class Responsibility**: The logic related to user interaction (`Console.ReadLine()`) is separated from the core functionality of the `BankAccount` class.
3. **Transaction Handling**: Introduced a `Transaction` class to better manage transactions.
4. **Improved Readability**: Cleaned up hard-coded values and made the code more reusable and extendable.

### Refactored Code:

```csharp
using System;
using System.Collections.Generic;

namespace BankApp
{
    public class BankAccount
    {
        // Properties of BankAccount
        public int AccountNumber { get; private set; }
        public string Owner { get; private set; }
        public decimal Balance { get; private set; }

        private static int accountNumberSeed = 1234567890;
        private List<Transaction> allTransactions = new List<Transaction>();

        // Constructor
        public BankAccount(string owner, decimal initialBalance)
        {
            AccountNumber = accountNumberSeed++;
            Owner = owner;
            if (initialBalance > 0)
            {
                Balance = initialBalance;
            }
            else
            {
                throw new ArgumentException("Initial balance must be positive");
            }
        }

        // Deposit Method
        public void Deposit(decimal amount)
        {
            if (amount <= 0)
            {
                throw new ArgumentException("Deposit amount must be positive");
            }

            Balance += amount;
            allTransactions.Add(new Transaction(TransactionType.Deposit, amount));
        }

        // Withdraw Method
        public void Withdraw(decimal amount)
        {
            if (amount <= 0)
            {
                throw new ArgumentException("Withdrawal amount must be positive");
            }

            if (Balance - amount < 0)
            {
                throw new InvalidOperationException("Insufficient funds for this withdrawal");
            }

            Balance -= amount;
            allTransactions.Add(new Transaction(TransactionType.Withdrawal, amount));
        }

        // Get account information
        public string GetAccountInfo()
        {
            return $"Account Number: {AccountNumber}, Owner: {Owner}, Balance: {Balance:C}";
        }
    }

    // Transaction class to track individual transactions
    public class Transaction
    {
        public TransactionType Type { get; private set; }
        public decimal Amount { get; private set; }
        public DateTime Date { get; private set; }

        public Transaction(TransactionType type, decimal amount)
        {
            Type = type;
            Amount = amount;
            Date = DateTime.Now;
        }
    }

    // Enum for transaction types
    public enum TransactionType
    {
        Deposit,
        Withdrawal
    }

    public class Program
    {
        public static void Main(string[] args)
        {
            // Example of using BankAccount
            BankAccount account = new BankAccount("Ali", 1000);
            Console.WriteLine(account.GetAccountInfo());

            // Simulate a deposit
            account.Deposit(500);
            Console.WriteLine(account.GetAccountInfo());

            // Simulate a withdrawal
            account.Withdraw(200);
            Console.WriteLine(account.GetAccountInfo());

            // Trying to withdraw more than balance (error case)
            try
            {
                account.Withdraw(2000);
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error: {ex.Message}");
            }
        }
    }
}

