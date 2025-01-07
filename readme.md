# Before Refactoring
```csharp
using System; 
using System.Collections.Generic; 
 
namespace classes 
{ 
    public class Program 
    { 
        public decimal Amount = 10000; 
        public DateTime Date DateTme.Now(); 
        public string Notes = “”;  
        public string Data= “”; 
        public List<Transaction> allTransactions = new List<Transaction>(); 
        public int accountNumberSeed = 1234567890; 
        public decimal Balance = 213123; 
        public string Number = “2222”; 
        public string Owner = “Ali”; 
        public char op = Convert.ToChar(Console.ReadLine()); 
        switch(op){ 
                case "a": 
                    Number = accountNumberSeed.ToString(); 
                    accountNumberSeed++; 
                    Owner = name; 
 
                    break; 
 
                case "b": 
                    Number = accountNumberSeed.ToString(); 
                    accountNumberSeed++; 
                    Owner = name; 
                    Balance = Balance + amount; 
                    allTransactions.Add(deposit); 
                    break; 
 
                case "c": 
                    Number = accountNumberSeed.ToString(); 
                    accountNumberSeed++; 
                    Owner = name; 
                    Balance = Balance - amount; 
                    allTransactions.Add(withdraw); 
                    break; 
 
                               default: 
                    break; 
            } 
 
} 
 
} 
```

----------------------------------------------------------------------------------------------------------------------------------------------------------------
# Refactored BankAccount Class

## Code Explanation

### Refactoring Steps:

1. **Encapsulation**: The `Balance`, `AccountNumber`, and `Owner` fields are now private and managed via public properties and methods.
2. **Class Responsibility**: The logic related to user interaction (`Console.ReadLine()`) is separated from the core functionality of the `BankAccount` class.
3. **Transaction Handling**: Introduced a `Transaction` class to better manage transactions.
4. **Improved Readability**: Cleaned up hard-coded values and made the code more reusable and extendable.

----------------------------------------------------------------------------------------------------------------------------------------------------------------
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

```
----------------------------------------------------------------------------------------------------------------------------------------------------------------

# Key Refactoring Improvements

## BankAccount Class:
1. **Encapsulation**: 
   - `Balance`, `AccountNumber`, and `Owner` are now private fields with public properties. This means that these fields are hidden from the outside, and access is provided through methods or properties to ensure better control over the data.
   
2. **Deposit() and Withdraw() Methods**: 
   - These methods ensure valid operations and modify the account balance. The `Deposit()` method adds money to the balance, while the `Withdraw()` method ensures that the withdrawal doesn't result in a negative balance.

## Transaction Class:
- A `Transaction` class has been introduced to track individual transactions (deposit and withdrawal). This makes the code more modular and easier to extend in the future. Each transaction stores its type (deposit or withdrawal), amount, and date.

## Error Handling:
- Exception handling ensures proper validation of inputs and raises appropriate errors for invalid operations. For example, if the withdrawal amount is greater than the balance, an exception is thrown, preventing the operation from completing and ensuring the system behaves as expected.

## Use of Enums:
- The `TransactionType` enum helps distinguish between deposit and withdrawal transactions for better clarity. It makes the code more readable and self-explanatory, as we use meaningful names instead of arbitrary values for transaction types.

## User Interaction:
- The `Program` class handles user input and interaction with the bank account. The `BankAccount` class is responsible for all the operations related to the account itself (like deposits, withdrawals, and balance management). This separation ensures that the logic for account management is independent of user input, improving the overall maintainability of the code.



