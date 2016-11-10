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
char ct[3] = {'c', 't', '\0'};    // array of `char`
char ghost[15] = "galloping";     // quoted string constant (string literal)
char * str = "galumphing";        // pointer-to-char to string address

/// prototype for C-style string
int c_in_str (const char* str, char ch);    // argument type: `char*`
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
</div>
<!-- /.panel-group -->

{% include links.html %}
