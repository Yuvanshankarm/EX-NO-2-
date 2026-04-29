## EX. NO:2 IMPLEMENTATION OF PLAYFAIR CIPHER

Program:
```
#include <stdio.h>
#include <string.h>
#include <ctype.h>

#define SIZE 3   // Changed from 5 to 3

void generateKeyMatrix(char key[], char matrix[SIZE][SIZE]) {
    int alpha[26] = {0};
    int i, k = 0;
    char current;

    // Remove duplicates and replace 'J' with 'I'
    for (i = 0; key[i] != '\0'; i++) {
        current = toupper(key[i]);

        if (current == 'J')
            current = 'I';

        // Allow only A-I for 3x3 matrix
        if (current < 'A' || current > 'I' || alpha[current - 'A'])
            continue;

        alpha[current - 'A'] = 1;
        key[k++] = current;
    }

    key[k] = '\0';

    // Fill matrix with key characters and remaining letters A-I
    i = 0;

    for (int row = 0; row < SIZE; row++) {
        for (int col = 0; col < SIZE; col++) {

            if (i < strlen(key)) {
                matrix[row][col] = key[i++];
            }
            else {
                for (char ch = 'A'; ch <= 'I'; ch++) {

                    if (alpha[ch - 'A'])
                        continue;

                    matrix[row][col] = ch;
                    alpha[ch - 'A'] = 1;
                    break;
                }
            }
        }
    }
}

void findPosition(char matrix[SIZE][SIZE], char ch, int *row, int *col) {

    if (ch == 'J')
        ch = 'I';

    for (int i = 0; i < SIZE; i++) {
        for (int j = 0; j < SIZE; j++) {

            if (matrix[i][j] == ch) {
                *row = i;
                *col = j;
                return;
            }
        }
    }
}

void processDigraph(char a, char b, char matrix[SIZE][SIZE],
                    char *resA, char *resB, int encrypt) {

    int row1, col1, row2, col2;

    findPosition(matrix, a, &row1, &col1);
    findPosition(matrix, b, &row2, &col2);

    if (row1 == row2) {

        *resA = matrix[row1][(col1 + (encrypt ? 1 : SIZE - 1)) % SIZE];
        *resB = matrix[row2][(col2 + (encrypt ? 1 : SIZE - 1)) % SIZE];
    }
    else if (col1 == col2) {

        *resA = matrix[(row1 + (encrypt ? 1 : SIZE - 1)) % SIZE][col1];
        *resB = matrix[(row2 + (encrypt ? 1 : SIZE - 1)) % SIZE][col2];
    }
    else {

        *resA = matrix[row1][col2];
        *resB = matrix[row2][col1];
    }
}

void preprocessText(char *text) {

    char temp[100] = {0};
    int k = 0;

    for (int i = 0; text[i]; i++) {

        if (isalpha(text[i])) {

            char ch = toupper(text[i]);

            if (ch == 'J')
                ch = 'I';

            // Only A-I allowed
            if (ch >= 'A' && ch <= 'I')
                temp[k++] = ch;
        }
    }

    temp[k] = '\0';
    strcpy(text, temp);
}

void encryptDecryptText(char *text, char matrix[SIZE][SIZE], int encrypt) {

    preprocessText(text);

    char result[100] = {0};

    int len = strlen(text), k = 0;

    for (int i = 0; i < len; i += 2) {

        char a = text[i];
        char b = (i + 1 < len) ? text[i + 1] : 'X';

        if (a == b) {
            b = 'X';
            i--;
        }

        char resA, resB;

        processDigraph(a, b, matrix, &resA, &resB, encrypt);

        result[k++] = resA;
        result[k++] = resB;
    }

    result[k] = '\0';

    strcpy(text, result);
}

void printMatrix(char matrix[SIZE][SIZE]) {

    printf("Key Matrix:\n");

    for (int i = 0; i < SIZE; i++) {

        for (int j = 0; j < SIZE; j++) {
            printf("%c ", matrix[i][j]);
        }

        printf("\n");
    }
}

int main() {

    char key[100], text[100], matrix[SIZE][SIZE];

    printf("Enter the key: ");
    fgets(key, sizeof(key), stdin);
    key[strcspn(key, "\n")] = '\0';

    printf("Enter text to encrypt: ");
    fgets(text, sizeof(text), stdin);
    text[strcspn(text, "\n")] = '\0';

    generateKeyMatrix(key, matrix);

    printMatrix(matrix);

    encryptDecryptText(text, matrix, 1);

    printf("Encrypted Text: %s\n", text);

    encryptDecryptText(text, matrix, 0);

    printf("Decrypted Text: %s\n", text);

    return 0;
}

```





Output:
 <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/631affe7-a393-4a6a-9fe6-2deee1a8ab84" />
