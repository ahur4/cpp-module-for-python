### نمونه ی ایجاد ماژول با استفاده از زبان سی پلاس پلاس برای پایتون

سلام خدمت دوستان عزیز.

خب در ابتدا میریم سراغ نوشتن کد های سی پلاس پلاسمون، نگران توضیح نباشید که بعد از کد توضیحاتشو ارائه میدم بهتون :

```cpp
// mymodule.cpp

#include <python3.10/Python.h>

// C++ function that adds two numbers
int add(int a, int b) {
    return a + b;
}

// Wrapper function to expose the add() function to Python
static PyObject* py_add(PyObject* self, PyObject* args) {
    int a, b;
    if (!PyArg_ParseTuple(args, "ii", &a, &b)) {
        return nullptr;
    }
    int result = add(a, b);
    return PyLong_FromLong(result);
}

// Method table for the module's functions
static PyMethodDef MyMethods[] = {
    {"add", py_add, METH_VARARGS, "Add two integers."},
    {nullptr, nullptr, 0, nullptr} // Sentinel
};

// Module definition
static struct PyModuleDef mymodule = {
    PyModuleDef_HEAD_INIT,
    "mymodule",   // Module name
    nullptr,      // Module documentation (optional)
    -1,           // Size of per-interpreter state of the module, or -1 if the module keeps state in global variables.
    MyMethods     // Method table
};

// Module initialization
PyMODINIT_FUNC PyInit_mymodule() {
    return PyModule_Create(&mymodule);
}
```

خب از کامنت هاش کلیت موضوع قابل مشاهدست ولی بریم برای توضیح.

`در این بخش :`

```cpp
#include <python3.10/Python.h>
```

با این خط کتابخانه های مورد نیاز برای ایجاد ماژول پایتون در سی پلاس پلاس رو به کدمون اضافه میکنیم، این خط حاوی تعاریف و توابع مورد نیاز برای استفاده از Python C API هست.

این بخش از کد :

```cpp
// C++ function that adds two numbers
int add(int a, int b) {
    return a + b;
}
```

یک تابع معمولی و ساده در زبان سی پلاس پلاسه که دو عدد رو در ورودی میگیره و جمع اونهارو به ما برمیگردونه.

با استفاده از تابع `py_add` :

```cpp
// Wrapper function to expose the add() function to Python
static PyObject* py_add(PyObject* self, PyObject* args) {
    int a, b;
    if (!PyArg_ParseTuple(args, "ii", &a, &b)) {
        return nullptr;
    }
    int result = add(a, b);
    return PyLong_FromLong(result);
}
```

بین تابع `add` که در زبان سی پلاس پلاس نوشته شده با پایتون ارتباط برقرار میکنیم.

که در کل کارکرد این بخش به این صورته که پارامترهای ورودی رو از پایتون میگیره و به تابع `add` میده و بعد ریسپانس رو از تابع `add` میگیره و به پایتون میده.

و از تابع `PyArg_ParseTuple` برای تبدیل و مدیریت پارامتر ها استفاده میکنه.

همچنین برای ثبت توابع نوشته شده در سی پلاس پلاس در ماژول پایتون از تابع `MyMethods` استفاده میکنیم :

```cpp
// Method table for the module's functions
static PyMethodDef MyMethods[] = {
    {"add", py_add, METH_VARARGS, "Add two integers."},
    {nullptr, nullptr, 0, nullptr} // Sentinel
};
```

حالا نوبت به ایجاد ساختار ماژول و شناسوندن اون به پایتونه.

ما با استفاده از تیکه کد زیر اطلاعاتی از قبیل نام ماژول، توابعی که در `MyMethods` اضافه کردیم و اطلاعات مرتبط با استفاده از ماژول رو مینویسیم :

```cpp
// Module definition
static struct PyModuleDef mymodule = {
    PyModuleDef_HEAD_INIT,
    "mymodule",   // Module name
    nullptr,      // Module documentation (optional)
    -1,           // Size of per-interpreter state of the module, or -1 if the module keeps state in global variables.
    MyMethods     // Method table
};
```

در نهایت با استفاده از کد زیر ماژولمون رو ایجاد میکنیم و اون رو به پایتون میسپاریم.

```cpp
// Module initialization
PyMODINIT_FUNC PyInit_mymodule() {
    return PyModule_Create(&mymodule);
}
```

حالا برای تبدیل این کد سی پلاس پلاس باید اون رو کامپایل کنیم و به پایتون معرفیش کنیم که از کامپایلر `g++` استفاده میکنیم.

دستور مورد نظر :  
`g++ -shared -o mymodule.so -fPIC mymodule.cpp -I /path/to/python/include/python3.x`

فلگ `-shared` به کامپایلر میگه که خروجی ایجاد شده یه کتابخونه پویا باشه یعنی با پسوند `.so` در لینوکس یا `.dll` در ویندوز.

در فلگ `-o` نام فایل خروجیمون رو مینویسیم.

این هم اسم فایل سی پلاس پلاسمونه که باید کامپایل بشه : `mymodule.cpp`

با استفاده از این فلگ هم `-I` به کامپایلر محل قرارگیری فایل های هدر پایتون رو نشون میدیم

در انتها بعد از کامپایل شدن کد سی پلاس پلاسمون ما یک فایل تحت عنوان `mymodule.so` داریم که اون رو در کنار فایل پایتونیمون قرار میدیم و مثل باقی ماژول های پایتون ازش استفاده میکنیم :

```python
import mymodule

print(mymodule.add(5, 10))
```

و خروجیمون جمع دو عدد خواهد بود.

Channel : @Ahur4\_Javid

Contact : @Ahur4
