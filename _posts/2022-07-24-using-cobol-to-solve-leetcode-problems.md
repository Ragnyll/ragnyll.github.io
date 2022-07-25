---
layout: post
title:  "Using COBOL to Solve Leetcode Problems: a Quixotic Challenge"
excerpt: "The solving modern problems with a dead language."
date: 2022-07-24 00:15:20 -0500
author: "Max Accardi"
---
&nbsp;&nbsp;&nbsp; COBOL, short for “common business-oriented language,” is a shambling husk of a language.  It has been described as a [“weak, verbose, and flabby language used by code grinders to do boring mindless things on dinosaur mainframes.”](http://catb.org/jargon/html/C/COBOL.html)  Compsci luminary Edsger Dijkstra suggested that teaching COBOL should [“be regarded as a criminal offense.”](https://quotepark.com/quotes/1816853-edsger-w-dijkstra-the-use-of-cobol-cripples-the-mind-its-teaching-s/)

&nbsp;&nbsp;&nbsp; Despite these flaws, COBOL serves as the backbone of the American economy. [ATM deposits and withdrawals; payroll systems; government personnel records; bank account and credit card transactions: by and large, these vital tasks run through COBOL code.](https://thenewstack.io/cobol-everywhere-will-maintain/)  When the COVID-19 pandemic caused a spike in unemployment claims, the increased traffic strained state unemployment systems - which are mostly written in, you guessed it, COBOL - and suddenly, in the year 2020, [there was a desperate need for COBOL programmers](https://stackoverflow.blog/2020/04/20/brush-up-your-cobol-why-is-a-60-year-old-language-suddenly-in-demand/).  IBM even sponsored an “[Intro to COBOL](https://www.youtube.com/watch?v=ZOLB4KqHmBs)” video in April 2020 to spark interest in the language.  Sadly, I have no statistics on whether it worked.

&nbsp;&nbsp;&nbsp; But those aren’t the reasons I decided to try to learn some COBOL.  Instead, my reasons are twofold.  First, I love dead languages.  Latin was my best subject in high school, and during the early days of COVID, for no particular reason, I learned to read and write Egyptian hieroglyphic.  I enjoy coding, but I’m not a software engineer by trade, so there’s nothing stopping me from wasting my free time learning useless languages for the fun of it.

&nbsp;&nbsp;&nbsp; Second, and relatedly, I think COBOL has weird and interesting design elements which tend to get ignored because people hate the language so much.  COBOL was intended to have English-like syntax (although it often doesn’t resemble English at all).  As a result, the formulae it uses to express things - like assigning values to variables, or initiating loops, or even basic variable declaration - are vastly different from virtually every modern language.  (For a complicated explanation of why, you can read about the “[Backus-Naur form](https://en.wikipedia.org/wiki/Backus%E2%80%93Naur_form),” a type of metasyntax many programming languages use, and of which the COBOL designers were not aware.)

&nbsp;&nbsp;&nbsp; For example, take variable declaration.  In most programming languages, to declare a variable, you give it a name and assign a value to it; the variable then fits into a designated type, either because you specify the type when you declare the variable, or because the interpreter or compiler assumes the type based on the value. For instance:

- Python: `hello = “hello”`
- Go: `var hello string  = “hello” // or, if you want the compiler to assume the type, hello := “hello”`
- C++: `string hello = “hello”;`

This is arguably a “[genus-differentia definition](https://en.wikipedia.org/wiki/Genus%E2%80%93differentia_definition)”: you identify the category to which the variable belongs - a category with certain pre-set constraints - and then set out the specific features of the variable within those constraints.

&nbsp;&nbsp;&nbsp; In COBOL, by contrast, variables do not have “types” (explicit or implicit), at least in the normal sense.  Instead, a variable is defined by a “picture clause,” which sets out, in generic terms, what the value contained in the variable must look like.  To declare the variable hello, you might write: `01 hello PIC A(5)`.  You could then add the characters `“h e l l o”` to the variable by adding `VALUE IS “hello”`, or you could do that later.  The “PIC A(5)” indicates that the variable will consist of up to 5 alphabetic characters.  This is what you might describe as an “ostensive definition,” in that you define the variable by pointing to what it looks like.  It strikes me as fundamentally different from the way most languages are designed.

&nbsp;&nbsp;&nbsp; And let’s say you want to change the value of a variable once it’s been declared.  In most programming languages, you’d write something logical, like `hello = “goodbye”`.  While you can do something similar in COBOL, the more COBOL-ic way to write it (if you’ll pardon that expression) is `MOVE “goodbye” TO hello`.

&nbsp;&nbsp;&nbsp; Here’s another example.  Virtually all modern languages allow programmers to initiate `for` and `while` loops, mostly by using the words “for” and “while.”  It’s also easy to create nested loops, and usually, the nesting is very obvious, because the second loop is literally within the first.  Not so in COBOL.  In COBOL, to create a loop, you have to first write the code through which you want to loop in a separate, named section of the program, and then write `PERFORM [named section]`, followed by either `X TIMES`, or `UNTIL [condition occurs]`.  The PERFORM section can itself contain PERFORM statements, enabling nested loops - but the structure of the loop is often highly obscure.  And this is actually a significant improvement from earlier versions of COBOL, where programmers used `GO TO` statements to move between blocks of code.

&nbsp;&nbsp;&nbsp; I decided it would be fun to see whether I could use COBOL to solve some simple Leetcode coding problems.  I started with “Palindrome Number,” which asks you to determine whether an integer, converted into a string, reads the same forwards and backwards.  For example, `111` is a palindrome because backwards, it also reads `111`; `-111` is not a palindrome, because backwards, it would read `111-`.  The harder version of the problem asks you to test if the integer is a palindrome without actually converting the integer into a string; and the harder version is what I will attempt.  With no further ado,  let’s write some COBOL!

&nbsp;&nbsp;&nbsp; (Fair warning: I was learning the language as I was doing the problem, so my code may not be, ahem, clean.  But then again, that might make it a better illustration of what COBOL usually looks like.)

&nbsp;&nbsp;&nbsp; A COBOL program is divided into “divisions,” some of which are mandatory.  As relevant here, the `DATA DIVISION → WORKING-STORAGE SECTION` is where you define your variables, and the `PROCEDURE DIVISION → MAIN SECTION` is where you write the body of your code.  Within the `PROCEDURE DIVISION`, you can also include other sections, which you can invoke within the `MAIN SECTION`.

&nbsp;&nbsp;&nbsp; My first step is to ascertain the integer to test:

```cobol
PROCEDURE DIVISION.
MAIN SECTION.
DISPLAY "Enter Number To Test: ".
ACCEPT Num.
```

&nbsp;&nbsp;&nbsp; (Num, by the way, is defined up in the WORKING-STORAGE SECTION as “01 Num PIC S9(10).  The ‘S’ indicates that it can be positive or negative.”)

&nbsp;&nbsp;&nbsp; Next, I tested for a few edge cases which are guaranteed to be palindromes or not be palindromes.  One-digit numbers are guaranteed to be palindromes; negative numbers and multiples of 10  are guaranteed to not be palindromes, since the input numbers have no leading zeros, and no number has a minus sign on both ends. Thus:

```
IF Num EQUAL TO ZERO
    DISPLAY "Palindrome"
    STOP RUN
ELSE
    CONTINUE
END-IF.

DIVIDE Num BY 10 GIVING result REMAINDER residue.

IF ZERO GREATER THAN Num OR residue EQUAL TO ZERO
    DISPLAY "Not Palindrome"
    STOP RUN
ELSE
    CONTINUE
END-IF.

PERFORM DIGIT-ADDING UNTIL dig_flag EQUAL TO 1.
MOVE ZERO TO dig_flag.

IF digits EQUAL TO 1
    DISPLAY "Palindrome"
    STOP RUN
ELSE
    CONTINUE
END-IF.

Notice the “DIGIT-ADDING” reference?  That invokes the following section, which follows the MAIN SECTION in the PROCEDURE DIVISION:

DIGIT-ADDING SECTION.
COMPUTE digits = digits + 1.
IF NUM < 10 ** digits
    COMPUTE dig_flag = dig_flag + 1
ELSE
    CONTINUE
END-IF.
```

&nbsp;&nbsp;&nbsp; The reason for this convoluted mess is that there doesn’t seem to be an elegant way to find the actual length of a variable in COBOL.  The language has a built-in `FUNCTION LENGTH`, but all variables have the length described in their `PIC` clauses; anything not filled by numbers or letters will be filled by zeroes or spaces.  So if I pass the input integer “12345” to Num, because Num’s picture clause reads `S9(10)`, Num’s length will still be 10 even though the integer is only 5 characters long.

&nbsp;&nbsp;&nbsp; Anyway, now we get to the heart of the program.  The method I’m using to determine whether Num is a palindrome - without treating it as a string and looking at each number individually - is to add `10**(digit-1)` until `Num` reaches a multiple of `10`, then store how many multiples it took in some kind of array.  So, for example `191` is a palindrome; we can add `10**0` to the 1s digit 9 times, then `10**1` to the 10s digit 1 time, then `10**2` to the 100s digit 9 times, yielding an array `[9,1,9]`.  (Because the number is being changed in place, I’ll have to add 1 to all of the array values after the first.)  If Num is a palindrome, the array will be a palindrome; if not, then it won’t.  So:

```
PERFORM DIGIT-CALC UNTIL counter_i EQUAL TO digits. (that is, perform DIGIT-CALC for each digit).
…
DIGIT-CALC SECTION.
COMPUTE i_store = counter_i + 1.
COMPUTE ten_store = 10 ** i_store.
MOVE ZEROES TO counter_j.
PERFORM DIGIT-WHILE UNTIL dig_flag EQUAL TO 1.
MOVE ZEROES TO dig_flag.
COMPUTE counter_i = counter_i + 1.
IF counter_i NOT EQUAL TO 1
    COMPUTE counter_j = counter_j + 1
ELSE
    CONTINUE
END-IF.
MOVE counter_j TO each_digit(counter_i).
…
DIGIT-WHILE SECTION.
COMPUTE Num = Num + 10**counter_i.
MOVE ZEROES TO result.
MOVE ZEROES TO residue.
DIVIDE Num BY ten_store GIVING result REMAINDER residue.
IF residue EQUAL TO 0
    COMPUTE dig_flag = 1
ELSE
    CONTINUE
END-IF.
COMPUTE counter_j = counter_j + 1.
```

&nbsp;&nbsp;&nbsp; This moves inward from each direction of the `digit_data` table, comparing the digits; if they’re unequal, the program indicates that the number isn’t a palindrome and ends (that’s the `STOP RUN` line).  Finally, after concluding the `DIGIT-COMPAR[isons]`, the program displays “Palindrome,” since that means that the digits in the table are palindromic.

&nbsp;&nbsp;&nbsp; Overall, my COBOL code is 108 lines long.  The Python version I submitted to Leetcode to make sure my method worked was 24 lines long, and even that could _easily_ be trimmed.  And, obviously, my solution was pretty naive.  Still, “verbose” seems like an apt way to describe programming in COBOL.

&nbsp;&nbsp;&nbsp; On the other hand, as I was working on the problem, I felt like I was using a potentially useful tool for the wrong purpose.  COBOL is great at some things.  It can perform complex arithmetic to a high degree of precision, which is vital to accomplish the things for which COBOL is actually used, like managing large volumes of financial transactions involving big numbers.  There’s a [YouTube video](https://www.youtube.com/watch?v=TQDHGswF67Q) where a programmer tries to prove that 0.1 + 0.2 = 0.3 to 17 digits of accuracy; he tries and fails in C, Python, and JavaScript, which all yield floating point errors.  He should have tried COBOL:

```cobol
IDENTIFICATION DIVISION.
PROGRAM-ID. Proof.

DATA DIVISION.
WORKING-STORAGE SECTION.
01 num-1 PIC V9(17) VALUE ZEROES.
01 num-2 PIC V9(17) VALUE ZEROES.
01 ans PIC V9(17) VALUE ZEROES.

PROCEDURE DIVISION.
ADD 0.1 TO num-1.
ADD 0.2 TO num-2.
ADD num-1 TO num-2 GIVING ans.
DISPLAY ans.
STOP RUN.
```

&nbsp;&nbsp;&nbsp; Can you guess what that program yields? That’s right: `.30000000000000000`.  I rest my case.
