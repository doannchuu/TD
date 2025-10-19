
#include <stdio.h>
#include <stdlib.h>

typedef struct Node {
    int data;
    struct Node *next;
} Node;

typedef struct Stack {
    Node *top;
} Stack;

// Khởi tạo stack rỗng
void Init(Stack *s) {
    s->top = NULL;
}

// Kiểm tra rỗng
int isEmpty(Stack *s) {
    return (s->top == NULL);
}

// Push: thêm phần tử vào đỉnh
int Push(Stack *s, int x) {
    Node *p = (Node*)malloc(sizeof(Node));
    if (p == NULL) {
        return 0; // thất bại
    }
    p->data = x;
    p->next = s->top;
    s->top = p;
    return 1; // thành công
}

// Peek: xem phần tử đỉnh mà không xóa
// trả -1 nếu rỗng (giả sử dữ liệu không âm - nếu cần, có thể dùng cờ lỗi)
int Peek(Stack *s, int *ok) {
    if (isEmpty(s)) {
        if (ok) *ok = 0;
        return -1;
    }
    if (ok) *ok = 1;
    return s->top->data;
}

// Pop: lấy phần tử đỉnh và xóa node, trả ra giá trị. Nếu rỗng trả -1 và ok=0
int Pop(Stack *s, int *ok) {
    if (isEmpty(s)) {
        if (ok) *ok = 0;
        return -1;
    }
    Node *p = s->top;
    int v = p->data;
    s->top = p->next;
    free(p);
    if (ok) *ok = 1;
    return v;
}

// Giải phóng toàn bộ node trong stack (important để tránh memory leak)
void Destroy(Stack *s) {
    Node *p = s->top;
    while (p != NULL) {
        Node *tmp = p;
        p = p->next;
        free(tmp);
    }
    s->top = NULL;
}

// Đổi số thập phân sang nhị phân và in ra màn hình
// Xử lý số 0 và số âm
void DoiSangNhiPhan_In(int n) {
    Stack s;
    Init(&s);

    if (n == 0) {
        printf("0\n");
        return;
    }

    int negative = 0;
    long nl = n; // để tránh overflow khi n = INT_MIN
    if (nl < 0) {
        negative = 1;
        nl = -nl; // chuyển sang dương
    }

    // Lưu các phần dư
    while (nl > 0) {
        int r = (int)(nl % 2);
        if (!Push(&s, r)) {
            fprintf(stderr, "Loi cap phat bo nho khi Push!\n");
            Destroy(&s);
            return;
        }
        nl /= 2;
    }

    // In kết quả
    if (negative) putchar('-');
    int ok;
    while (!isEmpty(&s)) {
        int bit = Pop(&s, &ok);
        if (ok) putchar('0' + bit);
        else break;
    }
    putchar('\n');

    Destroy(&s);
}

// Một phiên bản trả kết quả vào buffer (cần cấp bộ nhớ đủ lớn)
int DoiSangNhiPhan_Buffer(int n, char *buf, size_t buflen) {
    if (buf == NULL || buflen == 0) return 0;
    Stack s;
    Init(&s);

    if (n == 0) {
        if (buflen < 2) return 0;
        buf[0] = '0'; buf[1] = '\0';
        return 1;
    }
    int negative = 0;
    long nl = n;
    if (nl < 0) {
        negative = 1;
        nl = -nl;
    }

    while (nl > 0) {
        int r = (int)(nl % 2);
        if (!Push(&s, r)) {
            Destroy(&s);
            return 0;
        }
        nl /= 2;
    }

    size_t idx = 0;
    if (negative) {
        if (idx+1 >= buflen) { Destroy(&s); return 0; }
        buf[idx++] = '-';
    }
    int ok;
    while (!isEmpty(&s)) {
        if (idx+1 >= buflen) { Destroy(&s); return 0; } // không đủ chỗ
        int bit = Pop(&s, &ok);
        if (!ok) break;
        buf[idx++] = '0' + bit;
    }
    if (idx >= buflen) { Destroy(&s); return 0; }
    buf[idx] = '\0';
    Destroy(&s);
    return 1;
}

int main(void) {
    int n;
    printf("Nhap so thap phan (co the am): ");
    if (scanf("%d", &n) != 1) {
        printf("Nhap khong hop le!\n");
        return 1;
    }
    printf("Ket qua (in): ");
    DoiSangNhiPhan_In(n);

    // Ví dụ dùng buffer
    char out[128];
    if (DoiSangNhiPhan_Buffer(n, out, sizeof(out))) {
        printf("Ket qua (buffer): %s\n", out);
    } else {
        printf("Chuyen sang nhi phan (buffer) that bai!\n");
    }
    return 0;
}
Code hoàn chỉnh
#include <iostream>
#include <string>
using namespace std;

// ... (Tất cả các hàm đã triển khai ở trên)

// Hàm menu để test toàn bộ chức năng
void showMenu() {
    cout << "\n" << string(50, '=') << endl;
    cout << " CHƯƠNG TRÌNH DEMO STACK" << endl;
    cout << string(50, '=') << endl;
    cout << "1. Test các thao tác stack cơ bản" << endl;
    cout << "2. Chuyển đổi số thập phân → nhị phân" << endl;
    cout << "3. Demo với các số có sẵn" << endl;
    cout << "4. Hiển thị stack hiện tại" << endl;
    cout << "0. Thoát" << endl;
    cout << string(50, '=') << endl;
    cout << "Lựa chọn: ";
}

// Hàm test thao tác cơ bản
void testBasicOperations() {
    cout << "\n--- TEST THAO TÁC CƠ BẢN ---" << endl;
    Stack s;
    
    initStack(s);
    isEmptyDetailed(s);
    
    // Test push
    push(s, 10);
    push(s, 20);
    push(s, 30);
    displayStack(s);
    
    // Test pop
    int val;
    pop(s, val);
    displayStack(s);
    pop(s, val);
    displayStack(s);
    
    // Test stack rỗng
    while (pop(s, val)) {
        cout << "Đã lấy: " << val << endl;
    }
    
    isEmptyDetailed(s);
}

// Hàm demo với số có sẵn
void demoPredefinedNumbers() {
    cout << "\n--- DEMO VỚI SỐ CÓ SẴN ---" << endl;
    int testNumbers[] = {0, 1, 2, 5, 10, 15, 16, 255, 1024};
    int count = sizeof(testNumbers) / sizeof(testNumbers[0]);
    
    for (int i = 0; i < count; i++) {
        decimalToBinary(testNumbers[i]);
    }
}

int main() {
    Stack mainStack;
    initStack(mainStack);
    
    int choice;
    do {
        showMenu();
        cin >> choice;
        
        switch (choice) {
            case 1:
                testBasicOperations();
                break;
            case 2: {
                int number;
                cout << "Nhập số thập phân: ";
                cin >> number;
                decimalToBinary(number);
                break;
            }
            case 3:
                demoPredefinedNumbers();
                break;
            case 4:
                displayStack(mainStack);
                break;
            case 0:
                cout << "Kết thúc chương trình!" << endl;
                break;
            default:
                cout << "Lựa chọn không hợp lệ!" << endl;
        }
    } while (choice != 0);
    
    return 0;
}
