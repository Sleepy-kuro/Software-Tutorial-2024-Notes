#include <stdio.h>
#include <string.h>
#include <stdint.h>
#include <stdbool.h>


const char* const error_msg_decimal = "Error! That set of number is not a decimal number.\n";
const char* const error_msg_trinary = "Error! That set of number is not a trinary number.\n";
const char* const error_msg_duodecimal = "Error! That set of number is not a duodecimal number.\n";
const char* const error_msg_unsupported_system = "Error! The number system is not supported.\n";

const char* const msg_prompt_number = "Please enter a set of number:\n";
const char* const msg_prompt_current_number_system = "Please enter the current number system:\n";
const char* const msg_prompt_number_system_to_convert = "Please enter the number system you want the set of number be converted to:\n";
const char* const msg_output = "Output=";

bool is_valid_number(const char* number, int base) {
    for (int i = 0; number[i] != '\0'; i++) {
        if (base == 10 && (number[i] < '0' || number[i] > '9')) return false;
        if (base == 3 && (number[i] < '0' || number[i] > '2')) return false;
        if (base == 12 && !((number[i] >= '0' && number[i] <= '9') || (number[i] >= 'A' && number[i] <= 'B'))) return false;
    }
    return true;
}

uint64_t to_decimal(const char* number, int base) {
    uint64_t result = 0;
    for (int i = 0; number[i] != '\0'; i++) {
        result *= base;
        if (number[i] >= '0' && number[i] <= '9') result += number[i] - '0';
        else if (number[i] >= 'A' && number[i] <= 'B') result += number[i] - 'A' + 10;
    }
    return result;
}

void from_decimal(uint64_t number, int base, char* result) {
    if (number == 0) {
        result[0] = '0';
        result[1] = '\0';
        return;
    }

  int index = 0;
    char temp[65];
    while (number > 0) {
        int remainder = number % base;
        if (remainder < 10) temp[index++] = remainder + '0';
        else temp[index++] = remainder - 10 + 'A';
        number /= base;
    }

    for (int i = index - 1; i >= 0; i--) {
        result[index - 1 - i] = temp[i];
    }
    result[index] = '\0';
}

int main() {
    char number[65];
    int current_base, target_base;
    
    printf("%s", msg_prompt_number);
    scanf("%64s", number);

    printf("%s", msg_prompt_current_number_system);
    scanf("%d", &current_base);

    if (current_base != 3 && current_base != 10 && current_base != 12) {
        printf("%s", error_msg_unsupported_system);
        return 1;
    }

    if (!is_valid_number(number, current_base)) {
        if (current_base == 3) printf("%s", error_msg_trinary);
        else if (current_base == 10) printf("%s", error_msg_decimal);
        else printf("%s", error_msg_duodecimal);
        return 1;
    }

    printf("%s", msg_prompt_number_system_to_convert);
    scanf("%d", &target_base);

    if (target_base != 3 && target_base != 10 && target_base != 12) {
        printf("%s", error_msg_unsupported_system);
        return 1;
    }

    uint64_t decimal_value = to_decimal(number, current_base);
    char result[65];
    from_decimal(decimal_value, target_base, result);

    printf("%s%s\n", msg_output, result);

    return 0;
}
