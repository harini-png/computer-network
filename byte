#include<stdio.h>
#include <string.h>
#include <ctype.h>

int main()
{
    char word[100];
    int data[100][8], stuffed_output[200][8], destuffed_output[100][8];
    char recovered_word[100];

    int flag[8] = {0,1,1,1,1,1,1,0}; // ~
    int esc[8]  = {0,1,1,1,1,1,0,1}; // }

    int i, k;
    int stuffed_idx = 0;
    int destuffed_idx = 0;
    int stuffing_counter = 0;

    int choice;
    int byteNo, num_errors, e;

    printf("Enter a word (~ is FLAG, } is ESC): ");
    scanf("%99s", word);

    int n = strlen(word);
    for(i = 0; i < n; i++)
    {
        unsigned char ch = word[i];

        for(k = 7; k >= 0; k--)
        {
            data[i][k] = ch % 2;
            ch /= 2;
        }
    }

    // Add Starting Flag
    for(k = 0; k < 8; k++)
        stuffed_output[stuffed_idx][k] = flag[k];
    stuffed_idx++;

    // Perform Byte Stuffing
    for(i = 0; i < n; i++)
    {
        int isFlag = 1;
        int isEsc = 1;

        for(k = 0; k < 8; k++)
        {
            if(data[i][k] != flag[k])
                isFlag = 0;

            if(data[i][k] != esc[k])
                isEsc = 0;
        }

        if(isFlag || isEsc)
        {
            for(k = 0; k < 8; k++)
                stuffed_output[stuffed_idx][k] = esc[k];

            stuffed_idx++;
            stuffing_counter++;
        }

        for(k = 0; k < 8; k++)
            stuffed_output[stuffed_idx][k] = data[i][k];

        stuffed_idx++;
    }

    // Add Ending Flag
    for(k = 0; k < 8; k++)
        stuffed_output[stuffed_idx][k] = flag[k];
    stuffed_idx++;

    printf("\nOriginal Word: %s\n", word);
    printf("\nOriginal Binary:\n");
    for(i = 0; i < n; i++)
    {
        for(k = 0; k < 8; k++)
            printf("%d", data[i][k]);
        printf(" ");
    }

    printf("\n\nAfter Stuffing (Total Bytes = %d):\n", stuffed_idx);
    for(i = 0; i < stuffed_idx; i++)
    {
        for(k = 0; k < 8; k++)
            printf("%d", stuffed_output[i][k]);
        printf(" ");
    }

    printf("\n\nStatus : ");
    if(stuffing_counter)
        printf("Performed");
    else
        printf("Not Required");

    // 1. Error Selection Menu
    printf("\n\nChoose Error Injection Type:\n");
    printf("1. No Error\n");
    printf("2. Single Byte Error (Flips all 8 bits)\n");
    printf("3. Multiple Bytes Error (Flips all 8 bits per chosen byte)\n");
    printf("Enter Choice (1-3): ");
    scanf("%d", &choice);

    switch(choice)
    {
        case 1:
            printf("\nNo Error Introduced. Proceeding safely.\n");
            break;

        case 2:
            printf("Enter byte index to entirely flip (0-%d): ", stuffed_idx - 1);
            scanf("%d", &byteNo);

            if(byteNo >= 0 && byteNo < stuffed_idx)
            {
                // Invert every bit position within the targeted byte index array row
                for(k = 0; k < 8; k++)
                {
                    stuffed_output[byteNo][k] ^= 1;
                }
                printf("\nByte at index %d successfully flipped!\n", byteNo);
            }
            else
            {
                printf("\nInvalid Position! Proceeding without modifications.\n");
            }
            break;

        case 3:
            printf("Enter the total number of bytes you want to corrupt: ");
            scanf("%d", &num_errors);

            for(e = 0; e < num_errors; e++)
            {
                printf("\n[Error %d of %d] Enter byte index to entirely flip (0-%d): ", e + 1, num_errors, stuffed_idx - 1);
                scanf("%d", &byteNo);

                if(byteNo >= 0 && byteNo < stuffed_idx)
                {
                    // Invert every bit position within the targeted byte index array row
                    for(k = 0; k < 8; k++)
                    {
                        stuffed_output[byteNo][k] ^= 1;
                    }
                    printf("Byte at index %d flipped!\n", byteNo);
                }
                else
                {
                    printf("Invalid Position! Skipping this byte manipulation.\n");
                }
            }
            break;

        default:
            printf("\nInvalid Choice! Proceeding with no error introduction.\n");
            break;
    }

    if (choice == 2 || choice == 3)
    {
        printf("\nTransmitted Corrupted Frame:\n");
        for(i = 0; i < stuffed_idx; i++)
        {
            for(k = 0; k < 8; k++)
                printf("%d", stuffed_output[i][k]);
            printf(" ");
        }
        printf("\n");
    }

    // De-stuffing algorithm implementation logic
    // Start loop at index 1 and stop before the last index to strip frame flags
    for(i = 1; i < stuffed_idx - 1; i++)
    {
        int isEsc = 1;

        for(k = 0; k < 8; k++)
        {
            if(stuffed_output[i][k] != esc[k])
            {
                isEsc = 0;
                break;
            }
        }

        if(isEsc)
        {
            i++; // Skip escape token, capture payload directly next loop row iteration

            if (i < stuffed_idx - 1) {
                for(k = 0; k < 8; k++)
                    destuffed_output[destuffed_idx][k] = stuffed_output[i][k];
                destuffed_idx++;
            }
        }
        else
        {
            for(k = 0; k < 8; k++)
                destuffed_output[destuffed_idx][k] = stuffed_output[i][k];
            destuffed_idx++;
        }
    }

    // Convert bits back into chars
    for(i = 0; i < destuffed_idx; i++)
    {
        int ascii_val = 0;

        for(k = 0; k < 8; k++)
        {
            ascii_val = (ascii_val << 1) | destuffed_output[i][k];
        }

        recovered_word[i] = (char)ascii_val;
    }
    recovered_word[destuffed_idx] = '\0';

    printf("\nDestuffed Data:\n");
    for(i = 0; i < destuffed_idx; i++)
    {
        for(k = 0; k < 8; k++)
            printf("%d", destuffed_output[i][k]);
        printf(" ");
    }

    printf("\n\nRecovered Word : %s\n", recovered_word);

    // Verify Integrity
    if(strcmp(word, recovered_word) == 0)
    {
        printf("\nTransmission Successful. No Error Detected.\n");
    }
    else
    {
        printf("\nTransmission Error Detected!\n");
    }

    return 0;
}
