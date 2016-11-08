---
title: VNPY by VN.PY (Part I)
tags: [quant_trading, quant_api]
keywords: quant trading, python wrapper, quant api
summary: "VNPY is a Python-based open source quant trading platform. The methodology of Python API wrapper, and its design of event-based engine will be discussed in detail in this first part."
sidebar: computer_science_sidebar
permalink: computer_science_cpp_part_1.html
folder: computer_science
last_updated: Nov 7, 2016
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
| <span class="label label-success">C++98/03</span>  | existing C++ features, exceptions, <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.RTTI}}">RTTI</a>, templates, and <a href="#" data-toggle="tooltip" data-original-title="{{site.data.glossary.STL}}">STL</a> |
| <span class="label label-success">C++11</span> | more features, inconsistencies removal ( `-std=c++0x` ) |

{% include callout.html content="**Portability**: recompile the program without making changes and it runs without a hitch." type="primary" %}

### Compile and Link

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

### Integer Initialization and Literals

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
</div>
<!-- /.panel-group -->

{% include links.html %}
