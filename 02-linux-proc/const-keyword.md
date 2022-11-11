# const 修飾詞

const int val1=100, val2=50;

const int \*ptr=\&val1;

ptr=\&val2; (OK)

在這裡，ptr 是一個指向 const interger 的指標，ptr 是可以改變的， 但是 ptr 所指向的 integer 那個變數本身是不可以改變的 (const)。

int val1=100, val2=50; int const \*ptr=\&val1;

ptr=\&val2; (Error)

ptr 是指向 integer 的指標，ptr 所指的位置是不可改變的， 然而，ptr 所指向的 integer 變數內容是可改變的。
