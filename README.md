# COC-Project-Hangman-
// hangman.c











#include <stdio.h>
#include <string.h>
#include <ctype.h>
#include <stdlib.h>
#include <time.h>

#define MAX_WORD_LEN 50
#define MAX_GUESSES 6
#define WORD_BANK_SIZE 10

// Word bank (you can add more words)
const char *word_bank[WORD_BANK_SIZE] = {
    "CLASH", "MENTOR", "COMPUTER", "PROGRAM", "HANGMAN",
    "VARIABLE", "FUNCTION", "POINTER", "ARRAY", "ALGORITHM"
};

// Function to check and update guessed letters
int check_and_update(const char secret[], char display[], char guess) {
    int matched = 0;
    for (int i = 0; secret[i] != '\0'; ++i) {
        if (toupper(secret[i]) == toupper(guess) && display[i] == '_') {
            display[i] = toupper(secret[i]);
            matched = 1;
        }
    }
    return matched;
}

// Check if word is completely guessed
int is_completed(const char display[]) {
    for (int i = 0; display[i] != '\0'; ++i) {
        if (display[i] == '_') return 0;
    }
    return 1;
}

int main(void) {
    srand((unsigned)time(NULL)); // random seed
    const char *secret = word_bank[rand() % WORD_BANK_SIZE];
    int len = strlen(secret);

    char display[MAX_WORD_LEN];
    for (int i = 0; i < len; ++i) display[i] = '_';
    display[len] = '\0';

    char guessed_letters[27] = {0}; // stores guessed letters
    int guessed_count = 0;
    int lives = MAX_GUESSES;

    printf("Welcome to Hangman!\n");
    printf("You have %d wrong guesses allowed.\n\n", lives);

    while (lives > 0 && !is_completed(display)) {
        printf("Word: ");
        for (int i = 0; i < len; ++i) printf("%c ", display[i]);
        printf("\n");

        printf("Lives left: %d\n", lives);
        if (guessed_count > 0) {
            printf("Guessed letters: ");
            for (int i = 0; i < guessed_count; ++i)
                printf("%c ", guessed_letters[i]);
            printf("\n");
        }

        printf("Enter your guess (single letter): ");
        char input[100];
        if (!fgets(input, sizeof(input), stdin)) break;
        char guess = input[0];

        if (!isalpha(guess)) {
            printf("Please enter a valid alphabet letter.\n\n");
            continue;
        }

        guess = toupper(guess);
        int already = 0;
        for (int i = 0; i < guessed_count; ++i) {
            if (guessed_letters[i] == guess) {
                already = 1;
                break;
            }
        }
        if (already) {
            printf("You already guessed '%c'. Try another letter.\n\n", guess);
            continue;
        }

        guessed_letters[guessed_count++] = guess;

        if (check_and_update(secret, display, guess)) {
            printf("✅ Good guess!\n\n");
        } else {
            lives--;
            printf("❌ Wrong guess.\n\n");
        }
    }

    if (is_completed(display)) {
        printf("🎉 Congratulations! You won! The word was: %s\n", secret);
    } else {
        printf("😢 You lost! The word was: %s\n", secret);
    }

    return 0;
}
