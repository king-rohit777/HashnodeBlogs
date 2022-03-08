## NoJS - Creating a Calculator with only pure HTML and CSS.

I've recently been improving my CSS knowledge, and I couldn't think of a better way to put what I've learned to use than to create something that any rational person would use JavaScript for. This calculator was produced by myself. Here's how I put the calculator together.

## Radio buttons

User engagement comes first. Without JS, how can you tell if a user has pressed a button?** Radio** inputs are available. With


```
<input type="radio" name="x" id="q-1" /> 
<input type="radio" name="x" id="q-2" /> 
<label for="q-1">Quote 1</label>
<label for="q-2">Quote 2</label>

<p class="quote-1">...</p>
<p class="quote-2">...</p>
``` 
and
```
input, p { display: none }

#q-1:checked ~ .quote-1 { display: block; }
#q-2:checked ~ .quote-2 { display: block; }
```

Allow me to explain. The labels are linked to the **radio** buttons, so clicking on them acts as if you were clicking on their corresponding inputs. Because labels make style easier, they are preferred over using inputs directly. **A ~ B** will choose all items that match **B** and have a previous sibling that matches **A**, as **~ **is the generic sibling selector. This enables us to conceal the **p** by default and only show them when their linked input is enabled.

## CSS variables and counters

Now it's time to come up with a number. CSS variables are required. Create a property with a name that starts with a double hyphen**(--)** and a value that can be any CSS value. For instance, —color: brown or —digit: 3 are two examples. Simply use the **var** function to use the variables, as seen below. CSS counters, which can store and show numbers, will also be used. CSS counters are typically used for tasks like automatically numbering sections. As a result,

```
<input type="radio" name="theFirstDigit" id="set-to-1" /> 
<input type="radio" name="theFirstDigit" id="set-to-2" /> 
<input type="radio" name="theFirstDigit" id="set-to-3" /> 
<!-- insert labels -->

<div class="number-dsplay"></div>
```
and
```
#set-to-1:checked ~ div { --digit: 1; }
#set-to-2:checked ~ div { --digit: 2; }
#set-to-3:checked ~ div { --digit: 3; }

.number-display { counter-increment: digit var(--digit);  }
.number-display::after { content: counter(digit) }
```
The variable **--digit** is set inside the **div** when the radio buttons are selected. All of the children would inherit the value as well. We then increment the counter **digit***and display this value using produced content because we can't directly print the variable's value. Because counter values cannot be utilized in ***calc***, which we will examine in the future section, we must use CSS variables. We only need to repeat what we already have to gain extra digits. We may reduce CSS repetition by carefully layering the HTML and using intermediate variables. This can be seen in the image below.

```
<!-- digit inputs name="theFirstDigit -->

<div class="first-digit">
  <!-- digit inputs name="theSecondDigit" -->

  <div class="second-digit">
    <!-- ..and so on -->
    
  </div>
</div>
```
```
/* Include previous CSS */

.first-digit { --first-digit: var(--digit); }
.second-digit { --second-digit: var(--digit); }
```

As before, the inputs will set **--digit**, and each individual div will take that value and assign it to —first-digit, etc. This eliminates the requirement for the #set-to-1:checked CSS to be repeated for each digit.

## CSS Calc.

The **calc function** is available in CSS. Which enables you to perform computations (I'm as surprised as you). It can be used for a variety of things; for example, you can use it to calculate the breadth of something (100 percent - 95px). We may utilize it to determine our input numbers as well as the ultimate result in our scenario. Let's have a look at how to acquire the input number.

```
[name="theFirstDigit"]:checked ~ * .set-number { --number: var(--first-digit); }
[name="theSecondDigit"]:checked ~ * .set-number {  
  --number: calc(var(--first-digit)*10 + var(--second-digit)); 
}
[name="theThirdDigit"]:checked ~ * .set-number {  
  --number: calc(var(--first-digit)*100 + var(--second-digit)*10 + var(--third-digit)); 
}
/* and so on */
```
Because the CSS selector * matches all elements, the above CSS will look for an **a.set-number** descendant of any element that comes after a checked input with a specific name. Because it is later in the document, the second selector takes precedence over the first.

We can use a similar strategy to reach the final response if we add a set of inputs to choose the operation. Then it's merely a matter of capturing and showing the values in a counter. The **content** property can also accept a string, allowing us to display the operation to the user.

## @property and @counter

We can construct a working calculator using what we have so far. There is, however, one flaw: the absence of decimals. The problem is that integers can only be stored in counters. As a result, we must divide the total into integer and fractional components. The first thing we'll need is a mechanism to round numbers (counters won't work because they can't be entered into the calculator). We'll use the @property feature, which is still in beta. @property allows you to define a variable with type checking and control over whether or not the values are passed down to children. If we define a @property in this manner,

```
@property --integer {
    syntax: '<integer>';
    initial-value: 0;
    inherits: true;
}
```
then any value assigned to --integer will be rounded to the nearest integer. To show a number to 7 decimal places we will first do the following calculations. Here --number is defined outside

```
.number-display {
    --abs-number: max(var(--number), -1 * var(--number)); 
    /* By suptracting 0.5 we make sure that we round down */
    --integer: calc(var(--abs-number) - 0.5);
    --decimal: calc((var(--integer) - var(--abs-number)) * 10000000);

    --sign-number: calc(var( --abs-number) / var(--number));
}
```
We can increment counters with identical names by using --integer and --decimal. However, we are unable to display them immediately. The reason for this is that if we perform this for a number such as 1.005, the --integer value will be 1, and the --decimal value will be 5. We'll use a unique @counter-style to pad the decimal. We also need to use a @counter-style to display the negative sign, because we can't convince the system that we have a 'negative zero' with something like -0.5. We'll need the following to correctly display the number:

```
@counter-style pad-7 {
    system: numeric;
    symbols: "0" "1" "2" "3" "4" "5" "6" "7" "8" "9";
    pad: 7 "0"
}

@counter-style sign {
    system: numeric;
    symbols: "" "";
}

.number-display::after {
    content: counter(sign-number, sign) counter(integer) "." counter(decimal, pad-7);
}
```
The style is the counter function's second argument. The pad-7 style defines a standard number system with the exception that any value with fewer than 7 digits is padded with zeros. The sign style also uses a numeric system, but because the symbols have been left blank, it will only display a negative sign (when needed).

## Wrap up

These are the essential components of a calculator. There are still a few things to finish. Then there's the look (yes, I used CSS for its actual purpose too). You may have also noted that the current setup provides us with a different set of inputs for each digit of a number; we can use ~ , : checked and the display property to always show the labels of the following digit. The content can be broken down into different elements, allowing us to just display the decimal section when it is required.

Is there any way we can take this any further? We could give the customer the option of doing calculations with the result, but I don't believe it would be able to do so with an infinite number of values. You may get a long way by utilizing anything to generate the HTML. In principle, this might be made to work more like a scientific calculator. To do trigonometric functions, for example, we can take advantage of their symmetry and periodicity and then employ approximations. I believe the most difficult part would be using brackets, as I'm not aware of a means to dynamically add brackets to calc, so we'd have to create separate selectors and CSS for each circumstance.

## Conclusion

I made this calculator as a fun exercise and something goofy to do. My drive to make this was fueled by the absurdity. Nonetheless, I learned a lot while doing this. While I will still have to use Google to center text, I had a lot of fun doing it. If you have an idea for a ridiculous enterprise, I strongly advise you to pursue it. Why not, after all?

Check out the full code [here](https://github.com/king-rohit777/No_JS_Calculator)
