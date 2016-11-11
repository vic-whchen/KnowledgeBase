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

## Functions: Part 2

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
</div>
<!-- /.panel-group -->

{% include links.html %}
