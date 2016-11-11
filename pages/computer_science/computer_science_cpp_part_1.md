---
title: Introduction and Data Types
tags: [programming]
keywords: c++ programming, data types,
summary: "This part gives an brief introduction to C++ programming language. Two fundamental built-in types: integers and floating-point numbers are discussed here. More elaborate types, including arrays, structures, pointers, and string class are discussed as well."
sidebar: computer_science_sidebar
permalink: computer_science_cpp_part_1.html
folder: computer_science
last_updated: Nov 10, 2016
---

## Introduction

### Programming Paradigms in C++

| Paradigm | Supported | Features |
|----------:|:---------:|----------|
| **Procedural** | **C** | data (information) **+** algorithms (methods)<br/>structured programming, **top-down** design |
| **Object-Oriented** | **C++ class** | **bottom-up** design: from class to program<br/><a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.class_definition}}">class definition</a>, <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.polymorphism}}">polymorphism</a>, <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.inheritance}}">inheritance</a> |
| **Generic** | **C++ templates** | independence from particular **data type** |

### ISO C++ Standards

| Standard | Features |
|----------:|----------|
| <span class="label label-primary">C++98/03</span>  | existing C++ features, exceptions, <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.RTTI}}">RTTI</a>, templates, and <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.STL}}">STL</a> |
| <span class="label label-success">C++11</span> | more features, inconsistencies removal ( `-std=c++0x` ) |

{% include callout.html content="**Portability**: recompile the program without making changes and it runs without a hitch." type="primary" %}

### Compile & Link

| Phase | Functions |
|-------:|----------|
| **Compile** | `g++ my.cxx precious.cxx` / `g++ my.cxx precious.o` |
| **Build**/**Make** | compile in an **incremental** process |
| **Build All** | compile **all** source code files from scratch |
| **Link** | combine compiled source code with necessary **library** code |

### Header File Naming Conventions

| Type | Convention | Example | Comments |
|-----:|:----------:|:-------:|----------|
| C old | `.h` suffix | `math.h` | usable by C and C++ |
| C++ old | `.h` suffix | `iostream.h` | usable by C++ |
| **C++ new** | **-** | `iostream` | usable by C++ with `namespace std` |
| **Converted C** | `c` prefix | `cmath` | usable by C++, might use non-C features |


### Namespaces

| Type | Example | Location & Elements Availability |
|------|:-------:|----------------------------------|
| **`using` Directive** | `using namespace std;`| **above** & available to **every** functions /<br/>**inside** & available to **specific** function |
| **`using` Declaration** | `using std::cout` | **inside** function & **certain** elements available |
| **-** | **-** | when certain elements needed `std::cout` |

### Messaging Mechanics in C++

1. by using a **class method**: essentially a function call
2. by **redefining an operator**: e.g. `cout << "hello world!"`

{% include callout.html content="**Class**: describes all properties of a **data type** and **actions** that can be performed with it.<br/>**Object**: an **entity** created according to the description." type="primary" %}

<div markdown="span" class="bs-callout bs-callout-primary">C++ <strong>statement types</strong> include: <a href="#" data-toggle="tooltip" data-original-title="announces the name and the type of a variable used in a function">declaration statement</a>, assignment statement, <a href="#" data-toggle="tooltip" data-original-title="sends a message to an object, initiating some sort of action">message statement</a>, function call, <a href="#" data-toggle="tooltip" data-original-title="declares the return type for a function, along with the number and type of arguments the function expects">function prototype</a>, and return statement.</div>

### Naming Schemes

| Scheme | Rule |
|--------|----------|
| `__xxx` / `_Xxx` | reserved by implementation (**compiler** and resources) |
| `_xxx` | reserved by implementation (**global identifiers**) |
| `m_xxx` / `xxx_` | usually for **member names** |

## Fundamental Types

### Families of Basic Types

| Families | Types | <span class="label label-success">C++11</span> |
|-----:|------------|
| **Integral** | `signed char`, `short`, `int`, `long` | `long long` |
| | `unsigned char`, `unsigned short`, ... | `unsigned long long` |
| | `bool`, `char`, `wchar_t` | `char16_t`, `char32_t` |
| **Floating-point** | `float`, `double`, `long double` | |

### Integer Initialization & Literals

{% highlight cpp linenos %}
int owls = 101;   // traditional C initialization
int wrens(432);   // alternative C++ syntax

int emus{7};      // C++11 braced initializer for single-valued variable wo/ =
int rheas = {12}; // C++11 braced initializer for single-valued variable w/ =

int rocs = {};    // empty braces is initialized to 0
int psychics{};
{% endhighlight %}

{% include callout.html content="<span class=\"label label-success\">C++11</span> **List-Initialization** (with or without `=`) is a **universal initialization** syntax for **all** types. It also provides **better protection** against type conversion errors." type="primary"%}

{% include callout.html content="**Width** describs the amount of memory used for an integer. Header file `<climits>` contains information about integer **type limits**, e.g. `SHRT_MAX`, `INT_MAX`, `LONG_MIN`." type="primary" %}

{% include callout.html content="**Integer Literal**, or constant, is one written out explicitly. By **default**, `cout` displays integers in **decimal** form. Give `cout` messages by `dec`, `hex`, and `oct` manipulators to display integers in respective formats `cout << dec;`." type="primary" %}

### `char` Types & Literals

{% include callout.html content="`char` is **neither** signed **nor** unsigned by default. Choice left to the C++ implementation.<br/>`wchar_t` (for wide character type), can represent the **extended** character set." type="primary" %}

| Type | Prefix | Notes | Examples |
|------:|:------:|-------|----------|
| `wchar_t` | `L` | `wcin`, `wout` | `wchar_t bob = L'P';`<br/>`wcout << L"tall" << endl;` |
| <span class="label label-success">C++11</span> `char16_t` | `u` | unsigned 16b | `char16_t ch1 = u'q', ch2 = u'\u00F6';` |
| <span class="label label-success">C++11</span> `char32_t` | `U` | unsigned 32b | `char32_t ch3 = U'q', ch4 = U'\U0000222B';` |

{% include callout.html content="**Universal Character Names**, beginning with `\u` or `\U` (8 or 16 **hex** digits), represents **Unicode** and ISO 10646 characters. ( `int k\u00F6rper` )" type="primary" %}

| Literal | Examples |
|-----------|----------|
| single quotation marks | `'A'`|
| **symbolic** escape sequences | `\n`, `\t`, `\n` |
| **numeric** escape sequences | `'\032'`, `'\x1a'` (tied to a particular code) |

### Floating-Point Literals

{% highlight cpp linenos %}
1.234f        // `f` suffix: float constant
2.45E20F      // `F` suffix: float constant
2.345324E28   // double constant
2.2L          // `l` / `L` suffix: long double constant
{% endhighlight %}

### Type Conversions

| Cases | Comments |
|------:|----------|
| <span class="label label-success">C++11</span> **List-Initialization** | **narrowing** not permitted: `char c1 {31325};` **invalid** |
| **Expressions** | automatic <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.integral_promotions}}">integral promotions</a>, or from smaller to larger type|
| **Type Casts** | `long (thorn)`: type is **not** altered (C-style: `(long) thorn` )<br/>`static_cast<long> thorn`: variable type is **altered** |

### Automatic Type Deduction

{% highlight cpp linenos %}
std::vector<double> scores;   // useful for complicated types like STL
auto pv = scores.begin();
{% endhighlight %}

## Compound Types

### Arrays & Structures

{% include callout.html content="**Array** general declaration form is `typeName arrayName[arraySize]`.<br/>Empty `arraySize` forces compiler to count size (**not** recommend but safe for `char` array).<br/>Alternatives are **`vector`** template class and <span class=\"label label-success\">C++11</span>**`array`** template class in **C++ STL**." type="primary" %}

{% include callout.html content="**Structure** variables use **membership operator** `.` to access its members.<br/>Assignment operator `=` can be used for **same** structure types." type="primary" %}

{% highlight cpp linenos %}
float hotelTips[5] = {5.0, 2.5};  // initialize partially, others are 0
long  totals[500]  = {0};         // initialize all elements to 0

/// C++11 list-initialization
double earnings[4] {1.2e4, 1.6e4, 1.1e4, 1.7e4};  // optional `=`
unsigned int counts[10] = {};                     // all elements set to 0
{% endhighlight %}

{% highlight cpp linenos %}
/// structure declaration
struct inflatable
{
  std::string name;
  float volume;
  unsigned int SN : 4;    // bit field specifies # of bits a member occupies
};  // variables can be created simultaneously: `} var1, var2;`

/// structure variables declaration
struct inflatable goose;            // keyword `struct` required in C
inflatable vincent;                 // keyword `struct` not required in C++

/// C++11 list-initialization
inflatable duck {"Daphne", 0.12};   // optional `=`
{% endhighlight %}

{% include callout.html content="<span class=\"label label-success\">C++11</span> **List-initialization** protects against **narrowing**." type="danger" %}

### `string` Class

{% include callout.html content="**C-style string** is an array of `char` which ends with a **null character** `\0` explicitly. But it is implicitly included in quoted strings.<br/>**C++-style string** <span class=\"label label-primary\">C++98</span>, available in `<string>` header file and `std` namespace, is a `string` class variable or entity." type="primary" %}

{% highlight cpp linenos %}
/// C++11 list-initialization
char first_date[] = {"Le Chapon Dodu"};   // empty `[]` is safe in C-style
string third_date   {"The Bread Bowl"};   // C++ string object

/// concatenation & copy
str3 = str1 + str2;           // C++-style
strcpy(chararr3, chararr1);   // C-style method
strcat(chararr3, chararr2);   // available in `<cstring>` header file

/// size
int len1 = str1.size();       // object.method()
int len2 = strlen(chararrr1); // func(var)
{% endhighlight %}

{% include callout.html content="<span class=\"label label-success\">C++11</span> `u8` prefix indicates string literals of **UTF-8** encoding scheme.<br/><span class=\"label label-success\">C++11</span> **Raw string** uses `R` prefix and  delimiters of `\"(` and `)\"`, or `\"+*(` and `)+*\"`." type="primary" %}

### Unions & Enumerations

{% include callout.html content="**Union** holds different data types but only one type at a time.<br/>**Enumeration** creates symbolic constants. **Enumerators** are assigned integer by default.<br/><span class=\"label label-success\">C++11</span> Enumeration is extended with a form of **scoped enumeration**." type="primary" %}

{% highlight cpp linenos %}
/// union data format
union one4all {
  int int_val;
  long long_val;
  double double_val;    // as the size of `one4all`
};

/// enumeration data format
enum spectrum {         // enumeration: spectrum
  red, orange, blue     // enumerators: red, orange, blue
};
spectrum band;          // spectrum variable
band = blue;            // valid
band = spectrum(3);     // typecast 3 to type spectrum
{% endhighlight %}

### Pointers

{% include callout.html content="**Pointer** holds address of a value. while **pointer name** represents the location.<br/>**Indirect value** / **dereferencing operator** `*` yields the value at the location.<br/>C++ recommends `int* ptr` form to indicate a **pointer-to-int** type.<br/>C++ calls a pointer with value `0` as `null pointer`." type="primary"%}

{% highlight cpp linenos %}
int higgens = 5;
int* ptr = &higgens;      // `ptr`, not `*ptr`, is initialized by an address

int* pt1 = new int;       // `new` allocates memory in heap or free store
delete pt1;               // `delete` frees memory to which `ps` points

int * pt2 = new int [5];  // create dynamic array based on dynamic binding
delete [] pt2;            // free a dynamic array
{% endhighlight %}

{% include callout.html content="**Pointer arithmetic** increase the address by **number of bytes** of the data type.<br/>**Array name** is interpreted as an **address** (of first element) in C++.<br/>**Array notation** `[]` is equivalent to dereferencing a pointer." type="primary"%}

{% highlight cpp linenos %}
arr[i] == *(arr + i)
ptr[i] == *(ptr + i)
{% endhighlight %}

{% include callout.html content="For structures, use **`.`** with structure name, use **`->`** with pointer–to–structure." type="danger"%}

{% highlight cpp linenos %}
struct things { int good; int bad; };
things grubnose = {2, 13};    // use grubnose.good
things* pt = &grubnose;       // use pt->good
{% endhighlight %}

### `vector` & <span class="label label-success">C++11</span> `array` Template Class

{% highlight cpp linenos %}
#include <vector>
#include <array>

vector<typeName> vt(n_elem);  // dynamic size on heap/free storage
array<typeName, n_elem> arr;  // fixed size on stack, more efficient
{% endhighlight %}

{% include callout.html content="For `array` object, assignment to another array object is **allowed**.<br/>For **built-in** arrays, data has to be copied **element-by-element**." type="danger"%}

## Question & Answer

<div class="panel-group" id="accordion">
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseOne">What does directive <code class="highlighter-rouge">#include &lt;iostream&gt;</code> do?</a>
      </h4>
    </div>
    <div id="collapseOne" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
        Contents of <code class="highlighter-rouge">&lt;iostream&gt;</code> file substitutes for this directive before final compilation.
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseTwo">What does statement <code class="highlighter-rouge">using namespace std;</code> do?</a>
      </h4>
    </div>
    <div id="collapseTwo" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
        It makes definitions/elements in the <code class="highlighter-rouge">std</code> namespace available to all functions in a file or to a certain function.
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseThree">Why is <code class="highlighter-rouge">const</code> qualifier better than <code class="highlighter-rouge">#define</code> statement?</a>
      </h4>
    </div>
    <div id="collapseThree" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ol>
        <li>it specifies the type <strong>explicitly</strong></li>
        <li>it limits the definition to particular functions or files using C++'s <strong>scoping rules</strong></li>
        <li>it can be used with <strong>more</strong> elaborate types (structs, arrays).</li>
      </ol>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseFour">How does C++ decide what type a constant is?</a>
      </h4>
    </div>
    <div id="collapseFour" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li>by <strong>suffix</strong> (case insensitive):
          <ul>
            <li><code class="highlighter-rouge">L</code> for <code class="highlighter-rouge">long</code>, <code class="highlighter-rouge">U</code> for <code class="highlighter-rouge">unsigned int</code>, <code class="highlighter-rouge">ul</code> for <code class="highlighter-rouge">unsigned long</code> constant</li>
            <li><span class="label label-success">C++11</span> <code class="highlighter-rouge">LL</code> for <code class="highlighter-rouge">long long</code>, <code class="highlighter-rouge">ULL</code> for <code class="highlighter-rouge">unsigned long long</code> constant</li>
          </ul>
        </li>
        <li>by <strong>size</strong>: the <strong>smallest</strong> type it can hold when there is no suffix</li>
      </ul>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseFive">What safeguards does C++ provide to prevent from exceeding the limits of integer type?</a>
      </h4>
    </div>
    <div id="collapseFive" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <strong>NO</strong> automatic safeguards, but <code class="highlighter-rouge">&lt;climits&gt;</code> header file can be used to determine what the limits are.
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseSix">Are <code class="highlighter-rouge">char grade = 65;</code> and <code class="highlighter-rouge">char grade = 'A';</code> equivalent?</a>
      </h4>
    </div>
    <div id="collapseSix" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <strong>Only</strong> equivalent on a system using the <strong>ASCII</strong> code.
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseSeven">How to find out which character code <code class="highlighter-rouge">88</code> represents?</a>
      </h4>
    </div>
    <div id="collapseSeven" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li>print <code class="highlighter-rouge">char</code> type: <code class="highlighter-rouge">char c = 88; cout &lt;&lt; c &lt;&lt; endl;</code></li>
        <li>use <code class="highlighter-rouge">put()</code>: <code class="highlighter-rouge">cout.put(char(88));</code></li>
        <li>C++-style type cast: <code class="highlighter-rouge">cout &lt;&lt; char(88) &lt;&lt; endl;</code></li>
        <li>C-style type cast: <code class="highlighter-rouge">cout &lt;&lt; (char)88 &lt;&lt; endl;</code></li>
      </ul>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseEight">What are the two pointer notations for accessing structure members?</a>
      </h4>
    </div>
    <div id="collapseEight" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <figure class="highlight"><pre><code class="language-cpp hljs" data-lang="cpp"><table style="border-spacing: 0"><tbody><tr><td class="gutter gl" style="text-align: right"><pre class="lineno"><span class="hljs-number">1</span>
<span class="hljs-number">2</span>
<span class="hljs-number">3</span></pre></td><td class="code"><pre><span class="k"><span class="hljs-keyword">struct</span></span> <span class="n">inflatable</span><span class="o">*</span> <span class="n">ps</span> <span class="o">=</span> <span class="k"><span class="hljs-keyword">new</span></span> <span class="n">inflatable</span><span class="p">;</span>
<span class="n"><span class="hljs-built_in">cout</span></span> <span class="o">&lt;&lt;</span> <span class="n">ps</span><span class="o">-&gt;</span><span class="n">name</span> <span class="o">&lt;&lt;</span> <span class="n">end</span><span class="p">;</span>
<span class="n"><span class="hljs-built_in">cout</span></span> <span class="o">&lt;&lt;</span> <span class="p">(</span><span class="o">*</span><span class="n">ps</span><span class="p">).</span><span class="n">volume</span> <span class="o">&lt;&lt;</span> <span class="n"><span class="hljs-built_in">endl</span></span><span class="p">;</span><span class="w">
</span></pre></td></tr></tbody></table></code></pre></figure>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseNine">Whar are the four ways of managing memory for data in C++?</a>
      </h4>
    </div>
    <div id="collapseNine" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ol>
        <li><strong>Automatic Storage</strong>: automatic variables typically stored on <strong>stack</strong> (<strong>LIFO</strong>)</li>
        <li><strong>Static Storage</strong>: define <strong>externally</strong> (outside function) or use keyword <code class="highlighter-rouge">static</code></li>
        <li><strong>Dynamic Storage</strong>: on <strong>heap</strong> or free store by <code class="highlighter-rouge">new</code>/<code class="highlighter-rouge">delete</code> operator</li>
        <li><span class="label label-success">C++11</span> <strong>Thread Storage</strong>: <code class="highlighter-rouge">thread_local</code> storage class specifier</li>
      </ol>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseTen">For <code class="highlighter-rouge">vector</code>/<code class="highlighter-rouge">array</code> object, what's the difference between using <code class="highlighter-rouge">[]</code> and <code class="highlighter-rouge">at()</code>?</a>
      </h4>
    </div>
    <div id="collapseTen" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li>C++ does <strong>not</strong> check for out-of-range errors if using <code class="highlighter-rouge">[]</code> notation</li>
        <li><code class="highlighter-rouge">at()</code> member function <strong>catches</strong> errors during <strong>runtime</strong> and aborts (longer runtime)</li>
      </ul>
      </div>
    </div>
  </div>
  <!-- /.panel -->
</div>
<!-- /.panel-group -->

{% include links.html %}
