# C++ - Doxygen example
```c++
    /**
     * @file BankAccount.h
     * @author AI Assistant
     * @brief Defines the BankAccount class for managing bank account operations.
     * @version 1.0
     * @date 2025-07-03
     *
     * @copyright Copyright (c) 2025
     *
     */
    
    #ifndef BANK_ACCOUNT_H
    #define BANK_ACCOUNT_H
    
    #include <string>
    #include <stdexcept>
    
    /**
     * @class BankAccount
     * @brief Manages a simple bank account.
     *
     * @details This class provides basic functionalities for a bank account,
     * including depositing, withdrawing, and checking the balance. It is
     * designed as a demonstration of Doxygen documentation standards.
     *
     * @note This is a simplified model and does not handle concurrency or
     * complex financial operations.
     * @warning Improper use, like withdrawing negative amounts, is guarded
     * against but should be validated by the caller.
     */
    class BankAccount {
    public:
        /**
         * @enum AccountStatus
         * @brief Represents the current status of the bank account.
         */
        enum AccountStatus {
            Active,         ///< The account is active and operational.
            Frozen,         ///< The account is frozen; no transactions allowed.
            Closed          ///< The account is permanently closed.
        };
    
        /** @name Constructors and Destructors
         * @{
         */
    
        /**
         * @brief Constructs a new Bank Account object.
         *
         * @param owner The name of the account owner.
         * @param accNum The unique account number.
         * @param initialBalance The starting balance. Defaults to 0.0.
         * @throws std::invalid_argument if the initial balance is negative.
         */
        BankAccount(const std::string& owner, const std::string& accNum, double initialBalance = 0.0);
    
        /** @} */
    
        /** @name Financial Operations
         * @{
         */
    
        /**
         * @brief Deposits a specified amount into the account.
         *
         * @param amount The amount to deposit. Must be a positive value.
         * @return The new balance after the deposit.
         * @see withdraw()
         */
        double deposit(double amount);
    
        /**
         * @brief Withdraws a specified amount from the account.
         *
         * @param amount The amount to withdraw. Must be a positive value.
         * @return The new balance after the withdrawal.
         * @retval Throws std::out_of_range if the amount exceeds the current balance
         * or if the amount is negative.
         */
        double withdraw(double amount);
    
        /** @} */
    
    
        /** @name Accessors
         * @{
         */
    
        /**
         * @brief Retrieves the current balance of the account.
         * @return The current balance as a double.
         */
        double getBalance() const;
    
        /**
         * @brief Retrieves the account owner's name.
         * @return The owner's name as a constant string reference.
         */
        const std::string& getOwner() const;
    
        /**
         * @brief Retrieves the account's current status.
         * @return The current AccountStatus enum value.
         */
        AccountStatus getStatus() const;
    
        /** @} */
    
    private:
        std::string ownerName;      ///< Stores the full name of the account owner.
        std::string accountNumber;  ///< Stores the unique account number.
        double balance;             ///< Stores the current monetary balance.
        AccountStatus status;       ///< Stores the current status of the account.
    };
    
    #endif // BANK_ACCOUNT_H
```