---
title: Expressions and Functions
tags: [programming]
keywords: C++ programming, function
summary: "TODO"
sidebar: computer_science_sidebar
permalink: computer_science_cpp_part_2.html
folder: computer_science
last_updated: Nov 11, 2016
---

## Expressions

### Prefixing & Postfixing

* for **built-in** types: no difference between two forms
* for **user-defined** types (especially **class**): **prefix form** is more efficient

### Increment/Decrement Operators & Pointers

| Placement | Precedence | Association |
|-----------|:----------:|:-----------:|
| **postfix** increment & decrement | high | left to right |
| **prefix** increment & decrement, **dereferencing** operators | low | right to left |

{% highlight cpp linenos %}
double arr[5] = {21.1, 32.8, 23.4, 45.2, 37.4};
double *pt = arr;

double x = *++pt; // increment pointer, take the value
++*pt;            // increment pointed-to value
(*pt)++;          // increment pointed-to value
x = *pt++;        // increment pointer but dereference original location
{% endhighlight %}

### Type Aliases

| Method | Features |
|-------:|----------|
| `#define MTYPE char` | doesn't work if declaring a list of pointer variables |
| `typedef char MTYPE`| can handle more complex type **without** creating a new type |

### <span class="label label-success">C++11</span> Range-Based `for` Loop

{% highlight cpp linenos %}
double prices[5] = {4.99, 10.99, 6.87, 7.99, 8.49};

for (double x : prices)   // same work for each element of array or vector
  std::cout << x << std::endl;

for (double &x : prices)  // use reference variable to modify values
  x = x * 0.80;
{% endhighlight %}

{% include callout.html content="**`cctype` Library** is a package of **character**-related functions. Some common available functions include: `isalnum()`, `isalpha()`, `isdigit()`, `islower()`, `isupper()`, `ispunct()`, `isspace()`, `tolower()`, `toupper()`." type="primary" %}

## Functions: Passing Arguments

### Passing Arrays

{% include callout.html content="What actually passed is a **pointer**, but can be treated as an **array** inside function." type="danger" %}

{% highlight cpp linenos %}
/// prototype for 1D array
int sum_arr1d (int arr[], int n);         //  arr[i] == *(arr + i)
int sum_arr1d (int * arr, int n);         // &arr[i] ==   arr + i

/// prototype for 2D array
int sum_arr2d (int  arr[][4], int size);   // arr[r][c] == *(*(arr + r) + c)
int sum_arr2d (int (*arr)[4], int size);
{% endhighlight %}

{% include callout.html content="When passing an **ordinary variable**, function works with a **copy** (no `const` required).<br/>When passing an **array**, function works with the **original** ( `const` prevents modification)." type="primary" %}

{% highlight cpp linenos %}
const int* ptr = &oldage;
*ptr = newage;    // invalid: `*ptr` is read-only
ptr = &newage;    //   valid: `ptr` can be modified

int* const ptr2 = &oldage;
*ptr2 = newage;   //   valid: `*ptr2` can be modified
ptr2 = &newage;   // invalid: `prt2` is read-only
{% endhighlight %}

### Passing C-Style Strings

{% include callout.html content="What actually passed is the **address of the first character** in the string." type="danger" %}

{% highlight cpp linenos %}
/// three representations a string
char ct[3] = {'c', 't', '\0'};    // 1. array of `char`
char ghost[15] = "galloping";     // 2. quoted string constant (string literal)
char * str = "galumphing";        // 3. pointer-to-char to string address

/// prototype for C-style string
int c_in_str (const char* str, char ch);    // argument type: `char*`
{% endhighlight %}

### Passing Structure

{% include callout.html content="When passing structure, unlike array, **`&`** operator is **required** to get structure's address." type="danger" %}

{% highlight cpp linenos %}
/// structure & prototypes
struct travel_time { int hours; int mins; };

/// passing structure value
void show_time_val (travel_time tvt) {
  cout << tvt.hours << ":" << tvt.mins << endl;     // use `.` for structures
}
show_time_val (tvt);    // calling: pass value by structure name `tvt`

/// passing structure address
void show_time_add (const travel_time * tvt) {      // pointer with const
  cout << tvt->hours << ":" << tvt->mins << endl;   // use `->` for pointers
}
show_time_add (&tvt);   // calling: pass address by `&tvt`
{% endhighlight %}

### Passing `string` & `array` Objects

{% highlight cpp linenos %}
#include <string>
#include <array>

/// `string` object
void display_string (const string str);       // prototyping
string str = "Orion Nebula";
display_string(str);                          // calling

/// `array` object
void display_array  (array<string, N> arr);   // prototyping
array<string, N> arr = {"Spring", "Summer"};  // fixed size `N`
display_array(arr, N);                        // calling
{% endhighlight %}

### Passing Functions

{% highlight cpp linenos %}
double  pam  (int);   // function: pass name `pam` to get its address
double (*pf) (int);   // pointer-to-funtion: replace name with `(*pf)`
pf = pam;             // pointer-to-funtion `pf` points to function `pam()`
{% endhighlight %}

{% include callout.html content="When passing function, argument is function **name**, parameter is **pointer-to-function**.<br><span class=\"label label-success\">C++11</span> **automatic type deduction**: `double (*pf) (int) = pam;` **=** `auto pf = pam;` " type="danger" %}

{% highlight cpp linenos %}
double func (int time) { return 10.0 * time; }

/// use pointer-to-function to receive function
void run_func ( double (*pfunc) (int), int ntimes ) {
    for (int i = 0; i < ntimes; i++)
        cout << (*pfunc)(i) << endl;  // use pointer-to-function to call `fun(i)`
}

run_func (func, 5);    // pass function as an argument
{% endhighlight %}

{% highlight cpp linenos %}
/// ALL function signatures are SAME
/// can be handled by unified pointer-to-function type
const double * f1 (const double arr[], int n);
const double * f2 (const double [], int);
const double * f3 (const double *, int);

/// @brief: four ways of C++98 and C++11 in managing func1~func3

/// 1. one pointer-to-function
const double * (* ptof1) (const double *, int) = f1;
auto ptof2 = f2;    // C++11 feature
cout << * (*ptof1)(arr, N) << * ptof2(arr, n) << endl;

/// 2. an array of pointer-to-function
// auto p2 = {f1, f2, f3};  list-initialization NOT allowed for `auto`
const double * (* arr_ptof[3]) (const double *, int) = {f1, f2, f3};
for (int i = 0; i < 3; i++) {
    cout << * (arr_ptof[i](arr, n)) << endl;
}

/// 3. a pointer to a pointer-to-function (array first element)
auto p_to_ptof1 = arr_ptof;     // C++11 feature
const double * (* * p_to_ptof2) (const double *, int) = arr_ptof;
for (int i = 0; i < 3; i++) {
    cout << * (p_to_ptof2[i](arr, n)) << endl;
}

/// 4. a pointer to an array of pointer-to-function
auto pto_arrptof1 = &arr_ptof;  // C++11 feature
const double * (* (* pto_arrptof2)[3]) (const double *, int) = &arr_ptof;
for (int i = 0; i < 3; i++) {
    cout << * ((* pto_arrptof2)[i](arr, n)) << endl;
}
{% endhighlight %}

## Functions: Advanced Topics

### Reference Variables

{% highlight cpp linenos %}
/// reference, a compound type, is a name alias
int& rodents = rats; // initialization is required when being declared

/// reference is more often used as function parameters
double refcube(const double &ra);   // passing by reference
{% endhighlight %}

{% include callout.html content="non-`const` reference: **only** accepts non-`const` arguments with **matched** types<br/>`const` reference: accepts `const` & `non-const` arguments by using **temporary variable**" type="primary" %}

{% highlight cpp linenos %}
double ref_non   (double& value);       // only accept non-const `double`

double ref_const (const double& value); // accept non-const & const (recommended)
/// will generate temporary variable in two cases
ref_const (1.0 + dbl_val);  // case 1: correct type but non-`lvalue`
ref_const (int_val);        // case 2: convertible type
{% endhighlight %}

| Type | Recommended Passing (*no* mod) | Recommended Passing (*with* mod) |
|-----:|:------------------------------:|:--------------------------------:|
| **small built-in** | value | pointer |
| **array** | `const` pointer (**only** way) | pointer (**only** way) |
| **structure** | `const` pointer / `const` reference |  pointer / reference |
| **class object** | `const` reference (**standard** way) | reference |

### Default Arguments

| Type | Direction | Example |
|-----:|:---------:|---------|
| **Parameters** | add from right to left | `int harpo (int n, int m = 4, int j = 5);` |
| **Arguments** | assign from left to right | `beeps = harpo (8, 7);` |


### Function Overloading

{% include callout.html content="**Function Overloading** (polymorphism): same function name with different forms<br/>**Function Signature**: number, types, and order of parameters" type="primary" %}

{% include warning.html content="Compiler considers reference to a type and the type itself as **same** signature." %}

### Function Templates

| Template Type | Comments |
|--------------:|----------|
| **Original Template** | featured with **parameterized types** |
| **Explicit Specialization** | use different code for certain types |
| **Implicit Instantiation** | compiler generates fucntion definition for certain types|
| **Explicit Instantiation** | instruct compiler to create certain instantiation |

{% highlight cpp linenos %}
/// non-template function prototype:    highest priority
void Swap (job &, job &);

/// template function prototype:        lowest priority
template <typename T>
void Swap (T &, T &);

/// explicit specialization prototype:  middle priority
template <> void Swap<job> (job &, job &); // for `job` structure type

/// implicit instantiation
int rank1, rank2;
Swap (rank1, rank2); // generates `void Swap(int &, int &);`

/// explicit instantiation
template void Swap<double> (char &, char &); // inistantiation for `char`
Swap (char1, char2);
{% endhighlight %}

### Overloading Resolution

| Phase of Task | Target |
|:-----|------|--------|
| **1**. candidate functions | functions and template functions with **same name** |
| **2**. viable functions | correct number of parameters with **convertible** types |
| **3**. best viable | **exact match** > conversion (promotion > standard > user-defined) |

{% highlight cpp linenos %}
/// ambiguity is NOT allowed for exact match
/// in 3 special cases, best match can still be selected

recycle (ink);  // `ink` is a `blob` structure

/// case 1: non-`const` ptr/ref arguments for non-`const` ptr/ref parameters
///         only applied to data referred by pointer/reference
void recycle (blot &);        // call this
void recycle (const blot &);

/// case 2: non-template function better than template (or specialization)
void recycle (blot &);                    // call this
template <class Type> void recycle (T &);

/// case 3: more specialized template function is better
template <typename T> void recycle (T &);
template <> void recycle<blot> (blot &);  // call this

/// Force to use template function
lesser<> (dbl1, dbl2);     // use `double` type template
lesser<int> (dbl1, dbl2);  // use `int` type template with type cast
{% endhighlight %}

## Question & Answer

<div class="panel-group" id="accordion">
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseOne">Why prefixing is more efficient user-defined type class?</a>
      </h4>
    </div>
    <div id="collapseOne" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
        <strong>Prefix function</strong> increments a value and then returns it. <strong>Postfix</strong> version first <strong>stashes</strong> a copy, then increments and returns the stashed copy.
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseTwo">How to create a portable time-delay loop?</a>
      </h4>
    </div>
    <div id="collapseTwo" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <figure class="highlight"><pre><code class="language-cpp hljs" data-lang="cpp"><span class="cp"><span class="hljs-meta">#<span class="hljs-meta-keyword">include</span> <span class="hljs-meta-string">&lt;iostream&gt;</span></span>
<span class="hljs-meta">#<span class="hljs-meta-keyword">include</span> <span class="hljs-meta-string">&lt;ctime&gt;</span></span>
</span><span class="c1"><span class="hljs-comment">// use `clock()` function, `CLOCKS_PER_SEC` constant &amp; `clock_t` type</span>
</span><span class="kt"><span class="hljs-function"><span class="hljs-keyword">int</span></span></span><span class="hljs-function"> </span><span class="nf"><span class="hljs-function"><span class="hljs-title">main</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">()</span></span></span><span class="hljs-function">
</span><span class="p">{</span>
  <span class="k"><span class="hljs-keyword">using</span></span> <span class="k"><span class="hljs-keyword">namespace</span></span> <span class="n"><span class="hljs-built_in">std</span></span><span class="p">;</span>
  <span class="kt"><span class="hljs-keyword">float</span></span> <span class="n">secs</span><span class="p">;</span>
  <span class="n"><span class="hljs-built_in">cin</span></span> <span class="o">&gt;&gt;</span> <span class="n">secs</span><span class="p">;</span>
  <span class="kt"><span class="hljs-keyword">clock_t</span></span> <span class="n">delay</span> <span class="o">=</span> <span class="n">secs</span> <span class="o">*</span> <span class="n">CLOCKS_PER_SEC</span><span class="p">;</span>  <span class="c1"><span class="hljs-comment">// convert to clock ticks</span>
</span>  <span class="kt"><span class="hljs-keyword">clock_t</span></span> <span class="n">start</span> <span class="o">=</span> <span class="n">clock</span><span class="p">();</span>
  <span class="k"><span class="hljs-keyword">while</span></span> <span class="p">(</span><span class="n">clock</span><span class="p">()</span> <span class="o">-</span> <span class="n">start</span> <span class="o">&lt;</span> <span class="n">delay</span><span class="p">)</span>         <span class="c1"><span class="hljs-comment">// wait until time elapses</span>
</span>    <span class="p">;</span>
  <span class="k"><span class="hljs-keyword">return</span></span> <span class="mi"><span class="hljs-number">0</span></span><span class="p">;</span>
<span class="p">}</span></code></pre></figure>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseThree">What are the three steps in using a function?</a>
      </h4>
    </div>
    <div id="collapseThree" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ol>
        <li><strong>Prototypign</strong>: function <strong>interface</strong> (<strong>parameters</strong> number and type, return type)</li>
        <li><strong>Defining</strong>: code implementations</li>
        <li><strong>Calling</strong>: pass <strong>arguments</strong> and execute the function</li>
      </ol>
      <div class="bs-callout bs-callout-primary">
        <strong>Prototyping</strong> takes place during <strong>compile</strong> time and is termed <strong>static type checking</strong>.
      </div>
      <div class="bs-callout bs-callout-primary">
        <strong>Arguments</strong> (actual) passed to function. <strong>Parameters</strong> (formal) receive passed values.
      </div>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseFour">What's the restriction on type of return value of a function in C++?</a>
      </h4>
    </div>
    <div id="collapseFour" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li>return type has, or is <strong>convertible</strong> to, <code class="highlighter-rouge">typeName</code> type</li>
        <li>return value <strong>cannot</strong> be an <strong>array</strong> (all else is possible, even structures and objects)</li>
      </ul>
      <div class="alert alert-success" role="alert"><i class="fa fa-check-square-o"></i> <b>Tip:</b> Array can be returned as <strong>part of</strong> a structure or object.</div>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseFive">When do <code class="highlighter-rouge">int *arr</code> and <code class="highlighter-rouge">int arr[]</code> have identical meaning?</a>
      </h4>
    </div>
    <div id="collapseFive" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <strong>Only when</strong> used in a <strong>function header</strong> / <strong>prototype</strong>. Both mean <code class="highlighter-rouge">arr</code> is a <strong>pointer-to-int</strong>.
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseSix">Why don't we use <code class="highlighter-rouge">const</code> qualifier for arguments of fundamental types?</a>
      </h4>
    </div>
    <div id="collapseSix" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <p>Fundmental types is passed by <strong>value</strong> and works as a <strong>copy</strong> (original is <strong>already protected</strong>).</p>
      <div class="alert alert-success" role="alert"><i class="fa fa-check-square-o"></i> <b>Tip:</b> Use <code class="highlighter-rouge">const</code> with <strong>pointers</strong> to protect original <strong>pointed-to</strong> data from being altered.</div>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseSeven">What are two ways of using array ranges in functions?</a>
      </h4>
    </div>
    <div id="collapseSeven" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li>Pass <strong>number of elements</strong> as the second argument</li>
        <li>Concept “<strong>one past the end</strong>” in STL indicate an <strong>extent</strong></li>
      </ul>
      <figure class="highlight"><pre><code class="language-cpp hljs" data-lang="cpp"><table style="border-spacing: 0"><tbody><tr><td class="gutter gl" style="text-align: right"><pre class="lineno"><span class="hljs-number">1</span>
<span class="hljs-number">2</span>
<span class="hljs-number">3</span>
<span class="hljs-number">4</span>
<span class="hljs-number">5</span></pre></td><td class="code"><pre><span class="kt"><span class="hljs-function"><span class="hljs-keyword">void</span></span></span><span class="hljs-function"> </span><span class="n"><span class="hljs-function"><span class="hljs-title">sum_arr_1</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">(</span></span></span><span class="k"><span class="hljs-function"><span class="hljs-params"><span class="hljs-keyword">const</span></span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="kt"><span class="hljs-function"><span class="hljs-params"><span class="hljs-keyword">int</span></span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="o"><span class="hljs-function"><span class="hljs-params">*</span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="n"><span class="hljs-function"><span class="hljs-params">begin</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">,</span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="kt"><span class="hljs-function"><span class="hljs-params"><span class="hljs-keyword">int</span></span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="n"><span class="hljs-function"><span class="hljs-params">len</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">)</span></span>;</span>
<span class="kt"><span class="hljs-function"><span class="hljs-keyword">void</span></span></span><span class="hljs-function"> </span><span class="nf"><span class="hljs-function"><span class="hljs-title">sum_arr_2</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">(</span></span></span><span class="k"><span class="hljs-function"><span class="hljs-params"><span class="hljs-keyword">const</span></span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="kt"><span class="hljs-function"><span class="hljs-params"><span class="hljs-keyword">int</span></span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="o"><span class="hljs-function"><span class="hljs-params">*</span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="n"><span class="hljs-function"><span class="hljs-params">begin</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">,</span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="k"><span class="hljs-function"><span class="hljs-params"><span class="hljs-keyword">const</span></span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="kt"><span class="hljs-function"><span class="hljs-params"><span class="hljs-keyword">int</span></span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="o"><span class="hljs-function"><span class="hljs-params">*</span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="n"><span class="hljs-function"><span class="hljs-params">end</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">)</span></span></span><span class="hljs-function">  </span><span class="p">{</span>
    <span class="k"><span class="hljs-keyword">for</span></span> <span class="p">(</span><span class="n">ptr</span> <span class="o">=</span> <span class="n">begin</span><span class="p">;</span> <span class="n">ptr</span> <span class="o">!=</span> <span class="n">end</span><span class="p">;</span> <span class="n">ptr</span><span class="o">++</span><span class="p">)</span>
        <span class="p">...</span>
<span class="p">}</span><span class="w">
</span></pre></td></tr></tbody></table></code></pre></figure>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseEight">What are the trade-offs of passing structure by value and address?</a>
      </h4>
    </div>
    <div id="collapseEight" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li>By <strong>value</strong>: more time/memory, <strong>auto protection</strong> on original data, direct <code class="highlighter-rouge">.</code></li>
        <li>By <strong>address</strong>: saves time/memory, <strong>no</strong> auto protection without <code class="highlighter-rouge">const</code>, indirect <code class="highlighter-rouge">-&gt;</code></li>
      </ul>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseNine">For an array <code class="highlighter-rouge">arr</code>, what is the difference between <code class="highlighter-rouge">arr</code> and <code class="highlighter-rouge">&amp;arr</code>?</a>
      </h4>
    </div>
    <div id="collapseNine" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li><code class="highlighter-rouge">arr</code>: address of the <strong>first element</strong>, that is, <code class="highlighter-rouge">&amp;arr[0]</code></li>
        <li><code class="highlighter-rouge">&amp;arr</code>: address of the <strong>entire array</strong>, numerically same value but <strong>different types</strong></li>
      </ul>
      <div class="alert alert-success" role="alert"><i class="fa fa-check-square-o"></i> <b>Tip:</b> <code class="highlighter-rouge">arr</code> and <code class="highlighter-rouge">&amp;arr</code> require different <strong>deference times</strong>: <code class="highlighter-rouge">arr[0] == *arr == **&amp;arr</code></div>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseTen">What is the difference between <code class="highlighter-rouge">double (*pf)(int);</code> and <code class="highlighter-rouge">double *pf(int);</code>?</a>
      </h4>
    </div>
    <div id="collapseTen" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li><code class="highlighter-rouge">double (*pf)(int);</code>: <code class="highlighter-rouge">pf</code> is a <strong>pointer-to-function</strong>, the function returns <code class="highlighter-rouge">double</code></li>
        <li><code class="highlighter-rouge">double *pf(int);</code>: <code class="highlighter-rouge">pf()</code> is a function that returns a <strong>pointer-to-double</strong></li>
      </ul>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseEleven">How to understand <code class="highlighter-rouge">typedef double * (*p_fun) (int);</code>?</a>
      </h4>
    </div>
    <div id="collapseEleven" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <figure class="highlight"><pre><code class="language-cpp hljs" data-lang="cpp"><table style="border-spacing: 0"><tbody><tr><td class="gutter gl" style="text-align: right"><pre class="lineno"><span class="hljs-number">1</span>
<span class="hljs-number">2</span></pre></td><td class="code"><pre><span class="k"><span class="hljs-keyword">typedef</span></span> <span class="kt"><span class="hljs-keyword">char</span></span> <span class="o">*</span> <span class="n">p_char</span><span class="p">;</span>            <span class="c1"><span class="hljs-comment">// `p_char` is of type pointer-to-char</span>
</span><span class="k"><span class="hljs-keyword">typedef</span></span> <span class="kt"><span class="hljs-keyword">char</span></span> <span class="o">*</span> <span class="p">(</span><span class="o">*</span><span class="n">p_func</span><span class="p">)</span> <span class="p">(</span><span class="kt"><span class="hljs-keyword">int</span></span><span class="p">);</span>   <span class="o"><span class="hljs-comment">//</span></span><span class="hljs-comment"> </span><span class="err"><span class="hljs-comment">`</span></span><span class="n"><span class="hljs-comment">p_func</span></span><span class="err"><span class="hljs-comment">`</span></span><span class="hljs-comment"> </span><span class="n"><span class="hljs-comment">is</span></span><span class="hljs-comment"> </span><span class="n"><span class="hljs-comment">of</span></span><span class="hljs-comment"> </span><span class="n"><span class="hljs-comment">type</span></span><span class="hljs-comment"> </span><span class="n"><span class="hljs-comment">pointer</span></span><span class="o"><span class="hljs-comment">-</span></span><span class="n"><span class="hljs-comment">to</span></span><span class="o"><span class="hljs-comment">-</span></span><span class="n"><span class="hljs-comment">function</span></span><span class="w">
</span></pre></td></tr></tbody></table></code></pre></figure>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseTwelve">When and How to use inline functions?</a>
      </h4>
    </div>
    <div id="collapseTwelve" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <p><strong>Short</strong>, <strong>nonrecursive</strong> functions that can fit in one line are good candidates.</p>
      <figure class="highlight"><pre><code class="language-cpp hljs" data-lang="cpp"><table style="border-spacing: 0"><tbody><tr><td class="gutter gl" style="text-align: right"><pre class="lineno"><span class="hljs-number">1</span></pre></td><td class="code"><pre><span class="kr"><span class="hljs-function"><span class="hljs-keyword">inline</span></span></span><span class="hljs-function"> </span><span class="kt"><span class="hljs-function"><span class="hljs-keyword">double</span></span></span><span class="hljs-function"> </span><span class="nf"><span class="hljs-function"><span class="hljs-title">square</span></span></span><span class="hljs-function"> </span><span class="p"><span class="hljs-function"><span class="hljs-params">(</span></span></span><span class="kt"><span class="hljs-function"><span class="hljs-params"><span class="hljs-keyword">double</span></span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="n"><span class="hljs-function"><span class="hljs-params">x</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">)</span></span></span><span class="hljs-function"> </span><span class="p">{</span> <span class="k"><span class="hljs-keyword">return</span></span> <span class="n">x</span> <span class="o">*</span> <span class="n">x</span><span class="p">;</span> <span class="p">}</span><span class="w">
</span></pre></td></tr></tbody></table></code></pre></figure>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseThirteen">What types of data are <code class="highlighter-rouge">lvalue</code> or non-<code class="highlighter-rouge">lvalue</code>?</a>
      </h4>
    </div>
    <div id="collapseThirteen" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li><code class="highlighter-rouge">lvalue</code>: variable, array element, structure member, reference, dereferenced pointer</li>
        <li><strong>non</strong>-<code class="highlighter-rouge">lvalue</code>: <strong>literal</strong> constants (not quoted strings), <strong>expressions</strong> with multiple terms</li>
      </ul>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseFourteen">What are the strong reasons to declare <code class="highlighter-rouge">const</code> reference parameters?</a>
      </h4>
    </div>
    <div id="collapseFourteen" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li>it protects data from being altered inadvertently</li>
        <li>it allows reception from both <code class="highlighter-rouge">const</code> and non-<code class="highlighter-rouge">const</code> arguments</li>
        <li>it generates and uses temporary variable appropriately when needed</li>
      </ul>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseFifteen">How is returning a reference different from traditional return mechanism?</a>
      </h4>
    </div>
    <div id="collapseFifteen" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li>Returning a reference actually returns an <strong>alias</strong> for the referred-to variable.</li>
        <li>Traditional return mechanism <strong>copies</strong> returned value to a temporary location.</li>
      </ul>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseSixteen">How to avoid returning a reference to a non-existed memory location?</a>
      </h4>
    </div>
    <div id="collapseSixteen" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li>return a reference that was passed <strong>as an argument</strong> to the function</li>
        <li>use <code class="highlighter-rouge">new</code> to create new storage ( <code class="highlighter-rouge">delete</code> by <code class="highlighter-rouge">auto_ptr</code> or <span class="label label-success">C++11</span> <code class="highlighter-rouge">unique_ptr</code> )</li>
      </ul>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseSeventeen">What is the mechanism of function overloading and when to use it?</a>
      </h4>
    </div>
    <div id="collapseSeventeen" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <ul>
        <li><strong>Name decoration</strong>/<strong>mangling</strong> tracks overloaded functions (<strong>different</strong> signatures)</li>
        <li>For functions perform basically same task. Alternative is using <strong>default arguments</strong>.</li>
      </ul>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseEighteen">What are the limitations of template function and corresponding solutions?</a>
      </h4>
    </div>
    <div id="collapseEighteen" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <p>Template function might not be able to handle <strong>certain types</strong> (e.g., structures addition).</p>
      <ul>
        <li><strong>overload operator</strong> <code class="highlighter-rouge">+</code> for particular form of structure or class</li>
        <li>provide <strong>specialized</strong> template definitions for particular types</li>
      </ul>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseNineteen">How to determine the type of <code class="highlighter-rouge">xpy = (T1) x + (T2) y;</code> in a template function?</a>
      </h4>
    </div>
    <div id="collapseNineteen" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <p><span class="label label-success">C++11</span> <code class="highlighter-rouge">decltype</code> keyword: <code class="highlighter-rouge">decltype(expression) var;</code></p>
      <figure class="highlight"><pre><code class="language-cpp hljs" data-lang="cpp"><table style="border-spacing: 0"><tbody><tr><td class="gutter gl" style="text-align: right"><pre class="lineno"><span class="hljs-number">1</span>
<span class="hljs-number">2</span>
<span class="hljs-number">3</span>
<span class="hljs-number">4</span></pre></td><td class="code"><pre><span class="k"><span class="hljs-keyword">decltype</span></span><span class="p">(</span><span class="n">x</span><span class="o">+</span><span class="n">y</span><span class="p">)</span> <span class="n">xpy</span> <span class="o">=</span> <span class="n">x</span> <span class="o">+</span> <span class="n">y</span><span class="p">;</span>  <span class="c1"><span class="hljs-comment">// `xpy` is type of calculated type of `x + y`</span>
</span><span class="k"><span class="hljs-keyword">decltype</span></span><span class="p">(</span><span class="n">indeed</span><span class="p">(</span><span class="mi"><span class="hljs-number">3</span></span><span class="p">))</span> <span class="n">mol</span><span class="p">;</span>    <span class="c1"><span class="hljs-comment">// `mol` is type of return type of `indeed()`</span>
</span><span class="k"><span class="hljs-keyword">decltype</span></span><span class="p">((</span><span class="n">xx</span><span class="p">))</span> <span class="n">rh2</span> <span class="o">=</span> <span class="n">dbl</span><span class="p">;</span>   <span class="c1"><span class="hljs-comment">// `rh2` is type of `double &amp;`</span>
</span><span class="k"><span class="hljs-keyword">decltype</span></span><span class="p">(</span><span class="mi"><span class="hljs-number">100L</span></span><span class="p">)</span> <span class="n">ip2</span><span class="p">;</span>         <span class="o"><span class="hljs-comment">//</span></span><span class="hljs-comment"> </span><span class="err"><span class="hljs-comment">`</span></span><span class="n"><span class="hljs-comment">ip2</span></span><span class="err"><span class="hljs-comment">`</span></span><span class="hljs-comment"> </span><span class="n"><span class="hljs-comment">is</span></span><span class="hljs-comment"> </span><span class="n"><span class="hljs-comment">type</span></span><span class="hljs-comment"> </span><span class="n"><span class="hljs-comment">of</span></span><span class="hljs-comment"> </span><span class="n"><span class="hljs-comment">expression</span></span><span class="hljs-comment"> </span><span class="n"><span class="hljs-comment">type</span></span><span class="hljs-comment"> </span><span class="p"><span class="hljs-comment">(</span></span><span class="kt"><span class="hljs-comment">long</span></span><span class="p"><span class="hljs-comment">)</span></span><span class="w">
</span></pre></td></tr></tbody></table></code></pre></figure>
      </div>
    </div>
  </div>
  <!-- /.panel -->
  <!-- /.panel -->
  <div class="panel panel-default">
    <div class="panel-heading">
      <h4 class="panel-title">
        <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapseTwenty">How to determine the return type of a template function which <code class="highlighter-rouge">return (T1) x + (T2) y;</code>?</a>
      </h4>
    </div>
    <div id="collapseTwenty" class="panel-collapse collapse noCrossRef">
      <div class="panel-body">
      <p><span class="label label-success">C++11</span> <strong>trailing return type</strong>: <code class="highlighter-rouge">auto func() -&gt; decltype(expression)</code></p>
      <figure class="highlight"><pre><code class="language-cpp hljs" data-lang="cpp"><table style="border-spacing: 0"><tbody><tr><td class="gutter gl" style="text-align: right"><pre class="lineno"><span class="hljs-number">1</span>
<span class="hljs-number">2</span>
<span class="hljs-number">3</span>
<span class="hljs-number">4</span></pre></td><td class="code"><pre><span class="k"><span class="hljs-keyword">template</span></span> <span class="o">&lt;</span><span class="k"><span class="hljs-keyword">typename</span></span> <span class="n">T1</span><span class="p">,</span> <span class="k"><span class="hljs-keyword">typename</span></span> <span class="n">T2</span><span class="o">&gt;</span>
<span class="k"><span class="hljs-function"><span class="hljs-keyword">auto</span></span></span><span class="hljs-function"> </span><span class="n"><span class="hljs-function"><span class="hljs-title">gt</span></span></span><span class="hljs-function"> </span><span class="p"><span class="hljs-function"><span class="hljs-params">(</span></span></span><span class="n"><span class="hljs-function"><span class="hljs-params">T1</span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="n"><span class="hljs-function"><span class="hljs-params">x</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">,</span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="n"><span class="hljs-function"><span class="hljs-params">T2</span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="n"><span class="hljs-function"><span class="hljs-params">y</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">)</span></span></span><span class="hljs-function"> </span><span class="o"><span class="hljs-function">-&gt;</span></span><span class="hljs-function"> </span><span class="k"><span class="hljs-function"><span class="hljs-title">decltype</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">(</span></span></span><span class="n"><span class="hljs-function"><span class="hljs-params">x</span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="o"><span class="hljs-function"><span class="hljs-params">+</span></span></span><span class="hljs-function"><span class="hljs-params"> </span></span><span class="n"><span class="hljs-function"><span class="hljs-params">y</span></span></span><span class="p"><span class="hljs-function"><span class="hljs-params">)</span></span></span><span class="hljs-function"> </span><span class="p">{</span>
    <span class="k"><span class="hljs-keyword">return</span></span> <span class="n">x</span> <span class="o">+</span> <span class="n">y</span><span class="p">;</span>
<span class="p">}</span><span class="w">
</span></pre></td></tr></tbody></table></code></pre></figure>
      </div>
    </div>
  </div>
  <!-- /.panel -->
</div>
<!-- /.panel-group -->

{% include links.html %}
