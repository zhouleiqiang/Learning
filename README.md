指向引用的指针是非法的，因为引用只是一个别名，不存在真实的内存地址。
int a;
int *p = &a;
int & *pr = a;

指针的引用是合法的

int a;
int *p = &a;
int * &pr = p;




