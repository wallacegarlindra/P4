#include <stdio.h>
#include <string.h>

typedef struct { char name[50]; int price; int stock; } Product;
typedef struct { char name[50]; int points; } Member;

void checkProduct(Product db[], int size) {
    printf("\n--- Product Catalog ---\n");
    for (int i = 0; i < size; i++)
        printf("%d. %s - Rp%d (Stock: %d)\n", i + 1, db[i].name, db[i].price, db[i].stock);
}

void checkMember(Member db[], int size) {
    printf("\n--- Member Data ---\n");
    for (int i = 0; i < size; i++)
        printf("%d. %s - Points: %d\n", i + 1, db[i].name, db[i].points);
}

// Challenge 1
void updatePrice(Product *p, int newPrice) {
    p->price = newPrice;
}

// Challenge 3 (redeem points inside buyProduct)
void buyProduct(Product *p, Member *m, int qty) {
    if (p->stock < qty) {
        printf("\n[FAILED] Stock for %s (remaining: %d) is not enough!\n", p->name, p->stock);
        return;
    }
    p->stock -= qty;
    int total = p->price * qty;

    if (m != NULL && m->points >= 1000) {
        total = total - total / 10;      // 10% discount
        m->points -= 1000;
        printf("[INFO] %s redeemed 1000 points for a 10%% discount!\n", m->name);
    }

    printf("\n[TRANSACTION] %d %s sold. Total: Rp%d\n", qty, p->name, total);

    if (total >= 20000 && m != NULL) {
        m->points += 100;
        printf("  -> Member %s earned +100 points!\n", m->name);
    }
}

int main() {
    Product dbProduct[5] = {
        {"Soap", 5000, 10}, {"Shampoo", 15000, 5}, {"Bread", 12000, 8},
        {"Milk", 25000, 15}, {"Coffee", 22000, 20}
    };
    Member dbMember[2] = { {"Anisa", 1200}, {"Budi", 100} };

    int choice;
    do {
        printf("\n[1] View Products | [2] Buy Product | [3] Check Members | [4] Update Price | [5] Exit\n");
        printf("Choose: ");
        if (scanf("%d", &choice) != 1) {      // guard against non-numeric input
            while (getchar() != '\n');
            continue;
        }

        switch (choice) {
            case 1:
                checkProduct(dbProduct, 5);
                break;
            case 2: {
                int pi, mi, qty;
                checkProduct(dbProduct, 5);
                printf("Product number: "); scanf("%d", &pi);
                printf("Quantity: ");       scanf("%d", &qty);
                printf("Member number (1-2, 0 = non-member): "); scanf("%d", &mi);
                if (pi < 1 || pi > 5 || qty < 1 || mi < 0 || mi > 2) {
                    printf("Invalid input!\n");
                    break;
                }
                buyProduct(&dbProduct[pi - 1], mi == 0 ? NULL : &dbMember[mi - 1], qty);
                break;
            }
            case 3:
                checkMember(dbMember, 2);
                break;
            case 4: {
                int pi, newPrice;
                checkProduct(dbProduct, 5);
                printf("Product number: "); scanf("%d", &pi);
                printf("New price: ");      scanf("%d", &newPrice);
                if (pi < 1 || pi > 5 || newPrice <= 0) { printf("Invalid input!\n"); break; }
                updatePrice(&dbProduct[pi - 1], newPrice);
                printf("Price updated.\n");
                break;
            }
            case 5:
                printf("Goodbye!\n");
                break;
            default:
                printf("Unknown option.\n");
        }
    } while (choice != 5);

    return 0;
}
