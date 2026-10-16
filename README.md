# Password--Strength-Checker-
A Python-based cybersecurity tool that evaluates password strength, detects common weaknesses, provides security recommendations, and generates secure random passwords.

import string
import secrets
import getpass

COMMON = {
    "password", "password123", "12345678",
    "qwerty123", "admin123", "letmein"
}

def check_password(password):
    score = 0
    suggestions = []

    if len(password) >= 12:
        score += 30
    elif len(password) >= 8:
        score += 15
        suggestions.append("Use at least 12 characters.")
    else:
        suggestions.append("Password is too short.")

    checks = [
        (any(c.islower() for c in password), "lowercase"),
        (any(c.isupper() for c in password), "uppercase"),
        (any(c.isdigit() for c in password), "numbers"),
        (any(c in string.punctuation for c in password),
         "special symbols")
    ]

    for passed, name in checks:
        if passed:
            score += 15
        else:
            suggestions.append("Consider adding " + name)

    if len(password) >= 16:
        score += 10

    if password.lower() in COMMON:
        score = 0
        suggestions.append("Avoid common passwords.")

    if any(password[i] == password[i+1] == password[i+2]
           for i in range(len(password)-2)):
        score -= 15
        suggestions.append("Avoid repeated characters.")

    sequences = ["1234", "abcd", "qwerty", "4321"]
    if any(seq in password.lower() for seq in sequences):
        score -= 20
        suggestions.append("Avoid predictable sequences.")

    score = max(0, min(score, 100))

    if score >= 80:
        level = "STRONG"
    elif score >= 50:
        level = "MODERATE"
    else:
        level = "WEAK"

    return score, level, suggestions


def generate_password(length=16):
    characters = (string.ascii_letters +
                  string.digits + string.punctuation)

    while True:
        password = "".join(
            secrets.choice(characters)
            for _ in range(length)
        )

        score, level, suggestions = check_password(password)
        if level == "STRONG":
            return password


def main():
    while True:
        print("\n=== PASSWORD SECURITY TOOL 2.0 ===")
        print("1. Check password")
        print("2. Generate strong password")
        print("3. Exit")

        choice = input("Choose: ").strip()

        if choice == "1":
            password = getpass.getpass("Enter password: ")
            score, level, suggestions = check_password(password)

            print("Strength score:", score, "/ 100")
            print("Strength:", level)

            if suggestions:
                print("Suggestions:")
                for item in suggestions:
                    print("-", item)
            else:
                print("No basic weaknesses detected.")

        elif choice == "2":
            password = generate_password()
            print("Generated password:", password)
            print("Store it securely. Do not share it.")

        elif choice == "3":
            break
        else:
            print("Invalid choice.")


if __name__ == "__main__":
    main()
