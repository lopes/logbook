+++
title = "Handling Floating Point Precision in Spreadsheets"
date  = 2023-06-30
description = "Effective methods to handle floating point precision in spreadsheets."

[taxonomies]
tags = ["finance"]
+++

I was working on a spreadsheet for financial calculations when I encountered an issue.  At a certain point, I decided to create a conditional formatting to notify me when the value in a cell reached zero.  The spreadsheet contained numerous rows, and depending on certain conditions, values in a specific column would either be added or subtracted.  The problem came to my attention when I added the following values: `0.06`, `0.06`, `0.03`, and `0.15`.  Their sum equaled `0.34`, and at some stage, I subtracted this amount (which should have resulted in zero) from the sum. However, the conditional formatting did not function as intended.

I spent nearly an hour analyzing the formulas and conducting tests until I recalled the issue with floating-point precision.  After performing some tests related to this matter, I discovered that the expression `(sum(values) - 0.34)` was being evaluated as `0.000000000000000055511151231258`!  Clearly, it was not zero!

This problem arises because cloud-based spreadsheets utilize JavaScript, which lacks precision when handling floating-point numbers.  However, it's important to note that this issue is not exclusive to JavaScript, as other programming languages encounter similar problems.  Hence, in applications where precision is crucial, it is advisable to adopt an alternative approach for handling floating-point numbers.  One such approach involves utilizing two variables: One for the whole-number part and another for the decimal part.

While this alternative method enhances the reliability of the application, it also introduces additional complexity to the code, making it more challenging to maintain, or in the case of spreadsheets, more tedious to fill.  Imagine having to populate two cells every time you need to input a floating-point number.  It seems absurd, doesn't it?  Thankfully, there is another workaround available: Utilizing the `round()` function.  In this scenario, for example, I would use it to subtract a safe and acceptable number of decimals, such as ten, from the sum: `round(sum(values) - 0.34, 10)`.  This evaluation yields zero and resolves my problem.

Floating-point numbers can be a weakness if handled carelessly.  However, by employing the appropriate approach, it is possible to strike a balance between usability, maintainability, and the reliability of calculations.  Just keep this in mind when developing your solutions, and you'll be on the right track.  For more information about the floating-point problem or detailed explanations on this issue, please refer to the provided references.


## References
- [When zero - zero is not equal to zero?](https://support.google.com/docs/thread/93822123/when-zero-zero-is-not-equal-to-zero?hl=en)
- [Why does Google Spreadsheets says Zero is not equals Zero?](https://webapps.stackexchange.com/questions/80125/why-does-google-spreadsheets-says-zero-is-not-equals-zero)
- [Overcoming Javascript Numeric Precision Issues](https://www.avioconsulting.com/blog/overcoming-javascript-numeric-precision-issues)
