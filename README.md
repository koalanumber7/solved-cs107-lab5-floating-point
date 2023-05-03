Download Link: https://assignmentchef.com/product/solved-cs107-lab5-floating-point
<br>
In this lab, you will:

<ol>

 <li>experiment with oat/double types and observe their operational behavior</li>

 <li>explore the rounding inherent in oating point representation/arithmetic</li>

 <li>dissect the internals of the oating point format</li>

</ol>

Find an open computer and somebody new to sit with. Introduce yourself and share your favorite vegetable.

<h1>Lab exercises</h1>

<ol>

 <li>Get started.</li>

</ol>

Clone the repo by using the command below to create a lab5 directory containing the project les.

<h2>2. Basic behavior of oating point types</h2>

Compile and run the floats program to answer the following questions about the basic behavior of oating point types:

What are the values of FLT_MAX ? FLT_MIN ? FLT_DIG ? What are those values for type double?

Does anything strike you as weird about FLT_MIN -vs- INT_MIN , which is -2147483648 ? What happens on over ow to a value greater than max? What about divide by zero? How does divide by zero compare to those same operations for integer types? (run the divbyzero program to nd out!)

<h2>3. Float bits</h2>

Now, let’s go under the hood to look at the raw bits for a oat. An IEEE 32-bit oat is stored as:

where N is the sign bit (1 if negative), E is the 8-bit exponent (with a bias of 127), and S is the 23-bit signi cand, with an implicit leading “1.” for values. The sign bit is the bit position of most signi cance, the nal bit of the signi cand is the least. The floats program prints the raw bits for various oat values. Run the program and use its output to visualize the bit patterns and answer the following questions. There is also a neat online tool to interactively view oat bit patterns (https://www.hschmidt.net/FloatConverter/IEEE754.html).

What is the bit pattern for FLT_MAX ? FLT_MIN ? the value 1.0? the value 1.75?

How can you identify an exceptional value by its bit pattern?

How can you identify a negative value by its bit pattern?

How does a oat’s bit pattern change when multiplied by 2?

Consider the oat values that represent exact powers of 2. What do all their bit patterns have in common?

<h2>4. Practice converting normalized oats to decimal</h2>

As we discussed in class, converting oats to decimal is a mechanical process. Normalized oats have an exponent value that is neither all 0s nor all 1s. The steps to convert a normalized 32-bit oat to decimal are as follows (using the example 0 01111110 01000000000000000000000 ):

<ol>

 <li>Determine the sign ( n ) from the most signi cant bit (0 is positive, 1 is negative).</li>

 <li>Convert the 8-bit binary exponent to decimal, and subtract the bias (127 for 32-bit oats). E.g.,</li>

</ol>

01111110 is 126 in decimal, so the actual exponent ( e ) is 126-127 == -1 (you can use this converter website (http://web.stanford.edu/class/cs107/ oat/convert.html) to ease the process).

<ol start="3">

 <li>Intrepret the nal 23 bit signi cand ( s ) as a binary number 1.xxxx (e.g.,</li>

</ol>

01000000000000000000000 is intrepreted as 1.01000000000000000000000 . Convert this value to decimal (you can use the same site as above). For this example, s = 1.25.

<ol start="4">

 <li>Apply the formula: Decimal Value = (-1) <sup>n</sup> x s x 2 <sup>e </sup>. For our example: (-1) <sup>0</sup> x 25 x 2 <sup>-1</sup> = 0.625 .</li>

 <li>You can check your answer at this website (https://www.hschmidt.net/FloatConverter/IEEE754.html), where you can type in the binary value (e.g., 00111111001000000000000000000000 ) into the “Binary Representation” eld, and hit the enter</li>

</ol>

Fill in the table below on the lab checko form:

<table width="595">

 <tbody>

  <tr>

   <td width="194">Binary Value</td>

   <td width="32">Sign</td>

   <td width="149">Un­biased exponent in decimal</td>

   <td width="144">Decimal value of 1.significand</td>

   <td width="75">Decimal Value</td>

  </tr>

  <tr>

   <td width="194">0 10000011 10110000000000000000000</td>

   <td width="32"> </td>

   <td width="149"> </td>

   <td width="144"> </td>

   <td width="75"> </td>

  </tr>

  <tr>

   <td width="194">1 10001001 11001000010001000000000</td>

   <td width="32"> </td>

   <td width="149"> </td>

   <td width="144"> </td>

   <td width="75"> </td>

  </tr>

 </tbody>

</table>

<h2>5. Converting decimal to IEEE oating point representation</h2>

To convert decimals to oats, you basically reverse the process above. We will only worry about creating normalized oats. Follow the steps below to convert 0.4 decimal to a 32-bit oat:

<ol>

 <li>Convert the decimal value to a binary representation. This is easiest with the converter from above (http://web.stanford.edu/class/cs107/ oat/convert.html). Most often, this will result in a non-terminating binary value. For our example, 4d =</li>

</ol>

0.01100110011001100110011001100110011001100110011001100110011001b… .

<ol start="2">

 <li>Find the most signi cant 1 in the binary value, and use the following 23 bits after that 1. We may have to round, and the most common method is “round-to-nearest,” which adds 1 to the least signi cant bit if the truncated bits would have contributed more than one-half. In our example, 10011001100110011001100 comprise the 23 bits after the rst 1, and this leaves</li>

</ol>

11001100… remaining, which is bigger than half (0.1 is 1/2). Therefore, we round up, and the signi cand is 10011001100110011001101 .

<ol start="3">

 <li>To determine the exponent, gure out how many places the binary value from step (1) needs to be shifted to the <strong>left</strong> to produce a binary value in the range xxxxxx . This number is the unbiased exponent. To bias it, add 127 to the number (for 32-bit oats), and convert to binary (you can use the same calculator as in step (1)). For our example, we need to left shift -2 places, so our exponent will be -2 + 127 = 125 , or 1111101 in binary</li>

 <li>If the sign of the number is positive, the most signi cant bit will be 0, otherwise it will be 1.</li>

 <li>Concatenate the bits to get the oat representation: 0 1111101 10011001100110011001101 .</li>

 <li>If you check your answer, you might nd that we have lost some information! See the next section for the reason why.</li>

</ol>

<h2>6. Limits of a nite representation (nearest representable oats)</h2>

As a nite representation trying to model values drawn from an in nite range, oating point is inherently a compromise. Although some values are exactly representable, many others will have to approximated. Let’s drill down a bit to understand what precision you can expect. We will see that it is the number of bits dedicated to the signi cand that dictates the precision of what can be stored/computed.

Rounding during assignment from a constant. Many constants cannot be assigned to a oat without loss of precision. Floating point representation is based on binary decimal. If a given constant does not terminate when expressed as a binary decimal, it will have to be approximated. Consider the constant 0.4 we looked at in Part 5. This is 4/10 , or, in binary,

100/1010 . Apply division to that binary fraction and you’ll get a repeating binary decimal

0.01100 . There is no terminating sequence of powers-of-two that sums exactly to 0.4 , so no hope of exactly representing this value! Let’s follow what happens during these assignment:     float f = 0.4;  // not exact,  f rounded to nearest representable float

In Part 5, we were only able to use 23 bits of our signi cand (well, 24 because of the implicit leading 1), and we had to round. This left some information out of the representation.

Some constants are rounded because the binary decimal expansion (though terminating) requires more signi cand bits than are available in the data type. Consider these assignments:

// has terminating binary, but cannot be exactly represented as float         float f_inexact = 900000000.25;




// exact, 9.25 can be exactly represented as float         float f_exact = 9.25;

Convert the values to their oating point values, using the steps in part 5. You should nd out that the rst is not representable in 32-bit oating point format, but the second is.

<h2>7. Floating point arithmetic</h2>

An important lesson that comes from studying oats is learning why/how to arrange calculations to minimize rounding and loss of precision. Read over the code in the arith.c program. Execute it and observe the result of the sum_doubles function to see another surprising outcome. A sequence of doubles is summed forward and again backwards. The same values are included in the sum, but the order of operations is di erent. The result is not the same, and not just epsilon di erent either! Has the law of associativity been repealed? Explain what is going on. The relative error for oating point multiplication and division is small, but addition and subtraction have some gotchas to watch out for. For example, both addition and subtraction cannot maintain precision when applied to operands of very di erent magnitude. Two operands very close in magnitude can also cause a problem for subtraction or addition of values with opposite sign. If two oats have the same exponent and agree in the leading 20 bits of the signi cand, then only few bits of precision will remain after subtracting the two, and leaving just those few digits that were least signi cant too—double bummer! This event is called catastrophic cancellation (the catastrophe is so-called because you lose a large amount of signi cant digits all at once, unlike roundo error which occurs more gradually in the bits of low signi cance). A subtraction of nearly equal values ideally should be reformulated to avoid this cancellation.

<h2>8. Test BlueBook</h2>

If you brought your laptop, you can test the BlueBook program that we will be using for the midterm exam. Download this program (http://cs107.stanford.edu/BlueBook_MidtermPre.zip), unzip it completely, and then run the BlueBook.jar program. If you do not have a Java runtime, you may need to download it. If you are on a Mac and you get a message that says, “App can’t be opened because it is from an unidenti ed developer,” right-click on the exam and select “Open With -&gt; Jar Launcher.”

Once you have opened up BlueBook, you will see a login screen. Put your Name and Stanford SUNet

ID, and then click the honor code checkbox. Finally, scroll down and enter the password, which is examexam . At this point, you should be able to see the practice problems. When you are satis ed that

BlueBook is working, go ahead and click the “Finish Exam” button, and submit your practice, with your SUNet ID and password (i.e., the password you log onto Myth with). You should wait until you see an “Exam Submitted” message to ensure that you have properly submitted.

Fun and interesting further reading on oats:

The classic article What every computer scientist should know about oating-point arithmetic (http://download.oracle.com/docs/cd/E19957-01/806-3568/ncg_goldberg.html). (how’s that for a title that makes you feel compelled to read it?)

A little bit of history on the 1994 Pentium oating-point bug

(http://horstmann.com/unblog/2011-07-29/Capture-ch02_jfe2.png)that led to a half-billion dollar chip recall. Thanks to Cay Horstmann for this excerpt.

An excellent blog series on oating point intricacies

(http://randomascii.wordpress.com/2012/02/25/comparing- oating-point-numbers-2012edition/) written by Bruce Dawson.

Can you get rich by capitalizing on oating-point roundo ? More on the great salami slicing scam (http://en.wikipedia.org/wiki/Salami_slicing).