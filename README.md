//benyahia ayoub and baali anes

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_USERS 100

typedef struct {
    char name[100];
    int accountNumber;
    char password[50];
    float balance;
} Account;

Account accounts[MAX_USERS];
int totalAccounts = 0;

void loadAccounts() {
    FILE *file = fopen("accounts.txt", "r");
    if (file != NULL) {
        while (fscanf(file, "%s %d %s %f", accounts[totalAccounts].name,
                      &accounts[totalAccounts].accountNumber,
                      accounts[totalAccounts].password,
                      &accounts[totalAccounts].balance) == 4) {
            totalAccounts++;
        }
        fclose(file);
    }
}

void saveAccounts() {
    FILE *file = fopen("accounts.txt", "w");
    for (int i = 0; i < totalAccounts; i++) {
        fprintf(file, "%s %d %s %.2f\n", accounts[i].name, accounts[i].accountNumber,
                accounts[i].password, accounts[i].balance);
    }
    fclose(file);
}

int findAccount(int accNum) {
    for (int i = 0; i < totalAccounts; i++) {
        if (accounts[i].accountNumber == accNum) return i;
    }
    return -1;
}

void createAccount() {
    if (totalAccounts >= MAX_USERS) {
        printf("Max account limit reached.\n");
        return;
    }
    Account acc;
    printf("Enter your name: ");
    scanf("%s", acc.name);
    printf("Enter new account number: ");
    scanf("%d", &acc.accountNumber);
    printf("Enter a password: ");
    scanf("%s", acc.password);
    acc.balance = 0.0;

    if (findAccount(acc.accountNumber) != -1) {
        printf("Account number already exists.\n");
        return;
    }

    accounts[totalAccounts++] = acc;
    saveAccounts();
    printf("Account created successfully!\n");
}

void depositMoney(int index) {
    float amount;
    printf("Enter amount to deposit: ");
    scanf("%f", &amount);
    if (amount > 0) {
        accounts[index].balance += amount;
        saveAccounts();
        printf("Deposited successfully. New Balance: %.2f\n", accounts[index].balance);
    } else {
        printf("Invalid amount.\n");
    }
}

void transferMoney(int index) {
    int toAccount;
    float amount;
    printf("Enter destination account number: ");
    scanf("%d", &toAccount);
    int toIndex = findAccount(toAccount);
    if (toIndex == -1) {
        printf("Account not found.\n");
        return;
    }

    printf("Enter amount to transfer: ");
    scanf("%f", &amount);
    if (amount <= 0 || amount > accounts[index].balance) {
        printf("Invalid amount.\n");
        return;
    }

    accounts[index].balance -= amount;
    accounts[toIndex].balance += amount;
    saveAccounts();
    printf("Transferred successfully.\n");
}

void checkBalance(int index) {
    printf("Current Balance: %.2f\n", accounts[index].balance);
}

void deleteEmptyAccounts() {
    int i = 0;
    while (i < totalAccounts) {
        if (accounts[i].balance == 0) {
            for (int j = i; j < totalAccounts - 1; j++) {
                accounts[j] = accounts[j + 1];
            }
            totalAccounts--;
        } else {
            i++;
        }
    }
    saveAccounts();
    printf("Empty accounts deleted.\n");
}

void login() {
    int accNum;
    char pass[50];
    printf("Account number: ");
    scanf("%d", &accNum);
    printf("Password: ");
    scanf("%s", pass);

    int index = findAccount(accNum);
    if (index != -1 && strcmp(accounts[index].password, pass) == 0) {
        printf("------ You have successfully logged-in ----\n");
        int choice;
        do {
            printf("1. Deposit money\n2. Transfer money\n3. Check balance\n4. Logout\n");
            scanf("%d", &choice);
            switch (choice) {
                case 1: depositMoney(index); break;
                case 2: transferMoney(index); break;
                case 3: checkBalance(index); break;
                case 4: printf("Logging out...\n"); break;
                default: printf("Invalid choice.\n");
            }
        } while (choice != 4);
    } else {
        printf("Login failed. Incorrect account number or password.\n");
    }
}

int main() {
    loadAccounts();
    int choice;
    do {
        printf("------ Welcome to Our Bank ------\n");
        printf("1. Create Account\n2. Login to Account\n3. Delete Empty Accounts\n4. Quit\n");
        scanf("%d", &choice);
        switch (choice) {
            case 1: createAccount(); break;
            case 2: login(); break;
            case 3: deleteEmptyAccounts(); break;
            case 4: printf("Thank you for using our bank.\n"); break;
            default: printf("Invalid choice.\n");
        }
    } while (choice != 4);
    return 0;
}
