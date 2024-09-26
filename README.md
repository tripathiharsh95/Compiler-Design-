# Compiler-Design-
#include <stdio.h>
#include <ctype.h>
#include <string.h>

// Enum to define different token types
typedef enum {
    IDENTIFIER,
    CONSTANT,
    OPERATOR,
    COMMENT,
    STRING_LITERAL,
    DELIMITER,
    UNKNOWN
} TokenType;

// Function prototypes
void skipWhitespace();
void printToken(TokenType type, char *token);

int main() {
    char ch;        // Variable to store the current character being read
    char token[100]; // Array to store the current token being formed
    int index = 0;   // Index to track the position in the token array

    // Prompt the user to enter code
    printf("Enter code (end with EOF):\n");

    // Loop until EOF (End Of File) is encountered
    while ((ch = getchar()) != EOF) {
        // Skip any whitespace (spaces, tabs, newlines)
        if (isspace(ch)) {
            skipWhitespace();
            continue;
        }// Handle comments starting with '/'
        if (ch == '/') {
            token[index++] = ch;  // Add '/' to the token
            ch = getchar();       // Get the next character

            // Check if it's a single-line comment "//"
            if (ch == '/') {
                while ((ch = getchar()) != '\n' && ch != EOF);
                index = 0;  // Reset the token index
                continue;   // Move to the next iteration
            } 
            // Check if it's a multi-line comment "/*"
            else if (ch == '*') {
                token[index++] = ch;  // Add '*' to the token
                while ((ch = getchar()) != EOF) {
                    token[index++] = ch;
                    if (ch == '*' && (ch = getchar()) == '/') {
                        token[index++] = '/';
                        token[index] = '\0';  // Null-terminate the string
                        printToken(COMMENT, token);  // Print the comment token
                        index = 0;  // Reset the token index
                        break;  // Exit the comment loop
                    }
                }
continue;
            } else {
                ungetc(ch, stdin);  // Put the character back if it's not a comment
                token[index] = '\0';
                printToken(OPERATOR, token);  // Print the operator token
                index = 0;  // Reset the token index
                continue;
            }
        }

        // Handle string literals starting with '"'
        if (ch == '"') {
            token[index++] = ch;  // Add '"' to the token
            while ((ch = getchar()) != '"' && ch != EOF) {
                token[index++] = ch;
            }
            token[index++] = ch;  // Add the closing '"'
            token[index] = '\0';  // Null-terminate the string
            printToken(STRING_LITERAL, token);  // Print the string literal token
            index = 0;  // Reset the token index
            continue;
        }

        // Handle delimiters like ';', '(', ')', '{', '}', ','
        if (strchr(";,(){}", ch)) {
            token[index++] = ch;  // Add delimiter to the token
            token[index] = '\0';  // Null-terminate the string
            printToken(DELIMITER, token);  // Print the delimiter token
            index = 0;  // Reset the token index
            continue;
        }
// Handle operators like '+', '-', '*', '/', '=', etc.
        if (strchr("+-*/=<>!&|", ch)) {
            token[index++] = ch;  // Add the operator to the token
            ch = getchar();       // Get the next character

            // Check for two-character operators like '==', '!=', etc.
            if (strchr("=<>", ch)) {
                token[index++] = ch;
            } else {
                ungetc(ch, stdin);  // Put the character back if it's not part of the operator
            }
            token[index] = '\0';  // Null-terminate the string
            printToken(OPERATOR, token);  // Print the operator token
            index = 0;  // Reset the token index
            continue;
        }

        // Handle identifiers (variable names) and keywords
        if (isalpha(ch) || ch == '_') {
            token[index++] = ch;  // Add the character to the token
            while ((ch = getchar()) != EOF && (isalnum(ch) || ch == '_')) {
                token[index++] = ch;  // Continue building the identifier
            }
            ungetc(ch, stdin);  // Put the character back
            token[index] = '\0';  // Null-terminate the string
            printToken(IDENTIFIER, token);  // Print the identifier token
            index = 0;  // Reset the token index
            continue;} 
        // Handle numeric constants
        else if (isdigit(ch)) {
            token[index++] = ch;  // Add the digit to the token
            while ((ch = getchar()) != EOF && isdigit(ch)) {
                token[index++] = ch;  // Continue building the constant
            }
            ungetc(ch, stdin);  // Put the character back
            token[index] = '\0';  // Null-terminate the string
            printToken(CONSTANT, token);  // Print the constant token
            index = 0;  // Reset the token index
            continue;
        }

        // If none of the above, categorize it as an unknown token
        token[index++] = ch;
        token[index] = '\0';
        printToken(UNKNOWN, token);  // Print the unknown token
        index = 0;  // Reset the token index
    }

    return 0;
}

// Function to skip whitespace characters
void skipWhitespace() {
    char ch;
    while (isspace(ch = getchar()) && ch != EOF);
    ungetc(ch, stdin);
}// Function to print the token type and value
void printToken(TokenType type, char *token) {
    switch (type) {
        case IDENTIFIER: printf("IDENTIFIER: %s\n", token); break;
        case CONSTANT: printf("CONSTANT: %s\n", token); break;
        case OPERATOR: printf("OPERATOR: %s\n", token); break;
        case COMMENT: printf("COMMENT: %s\n", token); break;
        case STRING_LITERAL: printf("STRING_LITERAL: %s\n", token); break;
        case DELIMITER: printf("DELIMITER: %s\n", token); break;
        case UNKNOWN: printf("UNKNOWN: %s\n", token); break;
    }
}
