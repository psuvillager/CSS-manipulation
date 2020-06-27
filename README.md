# CSS-manipulation
- Three functions for adding and deleting CSS rulesets using JavaScript
- Info re: creating and manipulating stylesheets (in document.stylesheets)  
(Copied from: https://web.archive.org/web/20130709232042/http://www.hunlock.com/blogs/Totally_Pwn_CSS_with_Javascript)

__Totally Pwn CSS with Javascript__  
Filed: Fri, Feb 16 2007 under Programming|| Tags: .css1 javascript css dynamic
The last great frontier for a Javascript programmer is usually the direct manipulation of a stylesheet. While incredibly powerful tools, the documentation is sparse and the browsers rife with incompatibilities. You may be sending floating divisions flying across the screen playing videos of spanking kitties, but have you ever created a new stylesheet class on the fly? No? Read on!

__Tools for the Toolbox__  
We'll dive right in with three functions which will make working with stylesheets very trivial. After the functions are documented there will be a short tutorial and reference for those who want to go beyond the basic tools.

function getCSSRule(ruleName, deleteFlag) {               // Return requested style obejct
   ruleName=ruleName.toLowerCase();                       // Convert test string to lower case.
   if (document.styleSheets) {                            // If browser can play with stylesheets
      for (var i=0; i<document.styleSheets.length; i++) { // For each stylesheet
         var styleSheet=document.styleSheets[i];          // Get the current Stylesheet
         var ii=0;                                        // Initialize subCounter.
         var cssRule=false;                               // Initialize cssRule. 
         do {                                             // For each rule in stylesheet
            if (styleSheet.cssRules) {                    // Browser uses cssRules?
               cssRule = styleSheet.cssRules[ii];         // Yes --Mozilla Style
            } else {                                      // Browser usses rules?
               cssRule = styleSheet.rules[ii];            // Yes IE style. 
            }                                             // End IE check.
            if (cssRule)  {                               // If we found a rule...
               if (cssRule.selectorText.toLowerCase()==ruleName) { //  match ruleName?
                  if (deleteFlag=='delete') {             // Yes.  Are we deleteing?
                     if (styleSheet.cssRules) {           // Yes, deleting...
                        styleSheet.deleteRule(ii);        // Delete rule, Moz Style
                     } else {                             // Still deleting.
                        styleSheet.removeRule(ii);        // Delete rule IE style.
                     }                                    // End IE check.
                     return true;                         // return true, class deleted.
                  } else {                                // found and not deleting.
                     return cssRule;                      // return the style object.
                  }                                       // End delete Check
               }                                          // End found rule name
            }                                             // end found cssRule
            ii++;                                         // Increment sub-counter
         } while (cssRule)                                // end While loop
      }                                                   // end For loop
   }                                                      // end styleSheet ability check
   return false;                                          // we found NOTHING!
}                                                         // end getCSSRule 

function killCSSRule(ruleName) {                          // Delete a CSS rule   
   return getCSSRule(ruleName,'delete');                  // just call getCSSRule w/delete flag.
}                                                         // end killCSSRule

function addCSSRule(ruleName) {                           // Create a new css rule
   if (document.styleSheets) {                            // Can browser do styleSheets?
      if (!getCSSRule(ruleName)) {                        // if rule doesn't exist...
         if (document.styleSheets[0].addRule) {           // Browser is IE?
            document.styleSheets[0].addRule(ruleName, null,0);      // Yes, add IE style
         } else {                                         // Browser is IE?
            document.styleSheets[0].insertRule(ruleName+' { }', 0); // Yes, add Moz style.
         }                                                // End browser check
      }                                                   // End already exist check.
   }                                                      // End browser ability check.
   return getCSSRule(ruleName);                           // return rule we just created.
} 
getCSSRule -- Return a CSS rule
These are three simple and easy to use functions. getCSSRule(ruleName) will accept a CSS selector (classname, divname, etc) and if it exists will pass back the style object. Lets say you have a class called .global. To retrieve this style all you have to do is the following...

var global = getCSSRule('.global');
Global is now a pointer to the stylesheet rule for the class global and you can now manipulate it pretty much exactly like you would manipulate any getElementById object! For instance to give the class an overline and underline property just issue the following command…

global.style.textDecoration='underline overline';
This works for any defined selector. For instance if you have an ID match #global then you would retreive it via…

var global = getCSSRule('#global');
And of course it works on raw HTML elements. For instance to get your style for all the paragraph tags…

var global = getCSSRule('P');
getCSSRule goes by exact match. So if you have a rule for .global DIV you will need to specify .global DIV to get that rule, just .global won't cut it. If getCSSRule can't find the selector you're looking for it will return FALSE.

killCSSRule -- Delete a CSS rule
This is a VERY simple function. All it does is call getCSSRule with your ruleName and a delete flag. When getCSSRule finds your rule it will delete it and any objects on the page with that style will instantly become unstyled. Again this is an exact match function.

addCSSRule -- Create a new CSS rule
This is a really cool function. It basically accepts a rule name, creates an empty ruleset in the first stylesheet, and then returns the stylesheet object it created. Why is it cool? Well if you pass it a string p then you can instantly style every paragraph on the page. If you're creating a javascript distribution you don't have to distribute a css file with your javascript source, you can just create the stylesheet rules automatically with this function. Passing it .globalTwo would create a new classname called globalTwo and passing it #someDiv would let you style some division with an ID of someDiv. Here's an example:

var global2 = addCSSRule('.global2');
    global2.style.backgroundColor='green';
var someDiv = addCSSRule('#someDiv');
    someDiv.style.fontWeight='bold';
The caveat is that this works with the first stylesheet on the page regardless of the media type. So make sure your screen CSS appears as the first stylesheet if you want to use addCSSRule, otherwise -- while it may appear your new styles are going into a black-hole, your printouts may end up looking VERY funky indeed!

__Creating a StyleSheet Dynamically__  
If you have the need, you can create a stylesheet dynamically, on the fly, through javascript with the following code...

var cssNode = document.createElement('style');
cssNode.type = 'text/css';
cssNode.rel = 'stylesheet';
cssNode.media = 'screen';
cssNode.title = 'dynamicSheet';
document.getElementsByTagName("head")[0].appendChild(cssNode);
This will create a new style tag, assign it its attributes and then append the new object to the head tag of the current page. Since we assigned a title we can find our new sheet just by checking the title as we loop through the stylesheets.

__Loading a StyleSheet Dynamically__  
You can load an external stylesheet dynamically with the following code.

var cssNode = document.createElement('link');
cssNode.type = 'text/css';
cssNode.rel = 'stylesheet';
cssNode.href = 'http://www.somedomain.com/styles/FireFox.css';
cssNode.media = 'screen';
cssNode.title = 'dynamicLoadedSheet';
document.getElementsByTagName("head")[0].appendChild(cssNode);
This is much the same as our previous example but here the element we create is a 'link' and we specify an additional href attribute which gives the url of the external stylesheet. The moment the new node is appended to the "head" of the html page, the stylesheet is loaded and applied.

__Go forth in Style!__  
And there you have it, a few simple routines that let you totally pwn stylesheets in Javascript. There's room for improvement of course. getCSSRule can be modified to do substring matching and return an array of items, and addCSSRule can be modified to look for the media type of the stylesheet and even add a stylesheet if it has to.

If you're curious about __how Stylesheets are stored and accessed in the Document Object Model__, then read on!

document.stylesheets[]
Your stylesheets are stored in the document.styleSheets property. This is an array with each element of the array being one of the stylesheets associated with this page. document.styleSheets[0] would give you the first stylesheet on the page. Simple code which just flips through a pages styleSheets would look like this...

for (var i=0; i<document.styleSheets.length; i++) {}
The stylesheets have the following objects and properties:

Methods
addRule(selector, rules, [index])	IE	Adds a rule. By default rules are added to the end of the stylesheet but you can specify an index (starting at zero).
document.styleSheets[0].addRule(".demoClass", "background-color: blue")

insertRule(rule, index)	FireFox	This is the same as IE save the rule is bundle in one string. .demoClass {background-color: blue}.
removeRule([index])	IE	This will remove the first rule of the stylesheet if no index is specified or the rule at the index position.
deleteRule(index)	FireFox	This is the same as IE, it will remove the rule at the index position.
Properties
cssRules[]	FireFox	An array containing the rules for the current styleSheet
rules[]	IE	An array containing the rules for the current styleSheet
disabled	all	True if this stylesheet is disabled, false if its active.
href	all	The source URL of this stylesheet, if any.
imports[]	IE	An array of all the @import(ed) rules.
length	all	The number of stylesheets in the array
media	all	The media this stylesheet is active for (not always screen!)
ownerNode	FireFox	returns the DOM parent of this object, usually link or style.
owningElement	IE	returns the DOM parent of this object, usually link or style.
ownerRule	FireFox	If this sheet is a child via @import, this points to the parent.
parentStyleSheet	all	If this sheet is the product of a @page or @import, this property references the parent stylesheet.
title	all	If the style or link tag defined a title attribute, here it is.
type	all	If the style or link tag defined a type (text/css), here it is.
document.stylesheets[x].cssRules[]
Once you have a styleSheet selected, you can access the individual rules via the rules[] array for Internet Explorer or the cssRules[] array for Mozilla. For instance if you have a stylesheet defined as such…

<style>
   .global { background-color: white }
</style>
…then to reference .global you'd use the following...

   document.styleSheets[0].cssRules[0];
That would give you the first rule of the first stylesheet, which in this case happens to be .global. cssRules[] and rules[] have the following properties.

Properties
cssText	FireFox	Returns the styles of the present rule -- {float: left; text-align: right}
length	all	Returns the number of rules in this stylesheet
parentStyleSheet	all	Pointer to the styleSheet which contains these rules
selectorText	all	this is the name of the rule. For instance using our example above: .global
style	all	This is a style object that you can manipulate just like document.getElementById('someDiv').style
The really important items here are .length (which lets you know how many times you're going to have to loop to check each cssRule[], selectorText which lets you look for a specific classname (or other selector) like .global and style which lets you manipulate the rule just like any other javascript .style.rule='value' object.

Going back to our global example again. If we wanted to change its rule to be bold text then it's as simple as doing this...

document.styleSheets[0].cssRules[0].style.fontWeight='bold';
There are other methods and properties. For a seriously excellent overview of document.spreadheets[] I highly recommend you visit howtocreate.co.uk and scroll down to document.spreadsheets[]. You can click on a method or property for more information.

__Bringing it all together__  
Now that you know the methods and properties of the styleSheets[] and cssRules[] arrays it's pretty easy to see that our getCSSRule(ruleName) function just loops through each of the styleSheets and then through each cssRule[] in those sheets until it matches

document.styleSheets[x].cssRules[x].selectorText==ruleName
Once we have a match then we just return document.styleSheets[x].cssRules[x] which is a css object we can manipulate through the .style property just like any other div.

__Exploit the Rules!__  
Manipulating a HTML object's styles is powerful, but going beyond the one object and manipulating the stylesheets directly is even more powerful. With this you can instantly disable every input field of a form, or reactivate them. Hide every link, or make them re-appear -- in a size 72 font. Ok maybe that's not practical, but it sure sounds fun!

More importantly, the information in this article allows for javascript modules to be fully self-contained, needing nothing more from the end user than dropping in an include file. Once the script is loaded it can create all the divisions it needs to store the data and create all the styles and classes it needs to function.

It's one of those things where the more you use it, the more indespensible it becomes.

Happy Coding!
