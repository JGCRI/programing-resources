*Drawn in large part from:* [Best Practices for Scientific Computing (Wilson et al. 2014)](http://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.1001745)

# Programing Best Practices

## Table Of Contents

  [1. Introduction](#bp-intro)
  
  [2. Write code for humans not computers!](#bp-code-for-humans)
  
  [3. Tips for writing code for humans](#bp-writing-code)
  
  [4. Documenting code](#bp-documenting)
  
  [5. Plan Ahead (Writing Modular code)](#bp-modular)
  
  [6. Plan for Mistakes](#bp-errors)
  
  [7. Collaborate](#bp-collaborate)

  [8. Don't Reinvent The Wheel](#bp-reinvent)

    
## <a name="bp-intro">1. Introduction </a>
These practices will:

* Save you time overall
* Result in more reliable code
* Be better for your collaborators, particularly **Future You**

Before you start writing code, [Plan Ahead](#bp-modular) so you can write modular code.
  
## <a name="bp-code-for-humans">2. Write code for humans not computers!</a>

Code should be written knowing that someone, including **Future You**, will read with no background (or memory) of what was going on at the time.

* No Suspense! (This is good for a mystery novel, but BAD for coding and code documentation.)
* Except for the no suspense rule, think of this as writing a book/chapter/paragraph

### A few things we know about humans
* Have limited working memory
* Attention is easily drawn (or distracted) to inconsistent patterns
* Often impatient/time limited

### Therefore: 
* Don’t assume reader has read the previous 5 chapters. Reader needs to be able to understand the purposes and design of any particular segment without reading everything that came before.
* Don’t make reader hold more than a few items in memory at any given time
* Again, no suspense (reader shouldn’t’ have to wait until end to find out what is going on!)
* Make formatting and style consistent and distinctive

***
### ** Re-read the previous bullet points but insert “coder” instead of reader.
* The principle of "writing for humans" includes the human that is yourself.
* These techniques will help you code more effectively and accurately


## <a name="bp-writing-code">3. Writing good code (for humans)</a>

### Write Modular Code
A core concept of good coding is to break up code into smaller more digestible bits. This generally means breaking a task into a number of distinct, easily understood functions. This also means breaking a long block of code up into distinct blocks, even within one function. But if code gets too long, consider breaking this into a number of smaller functions.

The GCAM Data System (gcamdata) project is one example of both of these approaches. The overall design is highly modular (i.e., split in small chunks that produce specific outputs). Also, each code chunk follows a similar pattern of code blocks (e.g. purpose documented at the top, files read in, then processing, and output at the end), with a certain level of required documentation, including units, required.

[Example gcamdata "chunk"](https://github.com/JGCRI/gcamdata/blob/master/R/zchunk_L115.nh3_an_R_S_T_Y.R)

Note how the documentation in this example explains what is happening - you don't have to understand the details of each code segment to understand the structure and purpose of each part.

Other code projects will have different style guidelines, for example: [CEDS Example code](https://github.com/JGCRI/CEDS-dev/blob/master/code/module-A/A3.2.Adjust_Shipping_Fuel_Cons.R), but the general intent is similar.

We'll come back to [Writing Modular code](#bp-modular) below.

### Use Meaningful Names

The following code: <a name="bp-bad-code-example"></a>

```
x = 4.5
for i = 1, 5 {
```

is overly brief, and lacks comments; this does not help anyone understand what is going on.  Alternatively, consider the following code:

```
initial_fish_stock = 4.5 # billion tonnes of fish

# Loop over each ocean basin
for current_ocean_basin = 1, NUM_OCEAN_BASINS { 
```

which tells the reader much more about what is going on, but at the cost of very long names, which can themselves become distracting and hard to read. In addition, the comments focus on the _what_ instead of _why_. A good compromise might be:

```
ifs = 4.5 # initial fish stock, billion tonnes of fish

# Disaggregate fish stock into the various basins
for basin = 1, NUM_OCEAN_BASINS {
```

### Use Consistent Formatting Conventions

Each project should file a consistent set of coding guidelines, specifying formatting conventions for variables, functions, constants, documentation, indentation, and so on.

> Borrow a coding style guide from an existing project and, if needed, adapt that to your own project needs.

One example of a commonly used style denotes constants with capital letters:

`NUM_OCEAN_BASINS = 5`

With this as a standard, the user is reminded whenever this quantity is used that it is a constant and not a variable that can be changed (reducing the number of pieces of information that need to be in working memory). Also, even if this isn't enforced by the code (for example in R), the user is less likely to write:

`OCEAN_BASINS = OCEAN_BASINS + 1` - ***NO!***

Also, with this coding standard, a code reviewer is more likely to flag this for revision.

## <a name="bp-documenting">4. Documenting code </a>

### Embed documentation in the code
Most readers wont make it to a separate document, so to the extent possible embed documentation within the code. 

* Good comments should be succinct and clear, focusing on _why_ some operation is being done
* In the code examples linked above you'll note both longer documentation blocks describing the "big picture" (of either the code file or some segment of that file) and shorter documentation lines that describe specific steps.
* Long derivations and data descriptions may need to be in a separate document - keep those documents as close to the code as possible (e.g. in the same repository), but that doesn't eliminate the need for documentation within the code and embed permanent links (relative or absolute) to these documents if possible.

### Document design and purpose not mechanics
Good documentation is meant to help readers understand the code. Describe what a function is doing and write down reasons for key design decisions.

If you find yourself writing a long text describing the operation of a piece of code consider breaking that code up into smaller, more understandable pieces.

* This makes the code easier to understand
* This also will help avoid errors and problems buried in the middle of a complex calculation; better to "show your work" and break it into smaller pieces.

### Document your data structures
> Show me your flowcharts and conceal your tables, and I shall continue to be mystified. Show me your tables, 
> and I won’t usually need your flowcharts; they’ll be obvious.

<span style="text-align: right"> --Frederick Brooks, <em>The Mythical Man-Month</em></span>

The logic behind your algorithms is exposed in the sequence of statements in your program.  Not so, the layout of your
data structures.  At best, the person reading your code can infer them from the code that creates the structures; 
however, this code is likely to be far removed from the place where the structures are used, and it may not be gathered
together all in one place.  At worst, if the data structures are read in from files or generated in another package, 
there may be _no_ way, short of inspecting them in a debugging session, to decipher them.  Therefore, always describe 
the format and layout of data structures that are being passed into or returned from a function, or read or written
to an external source.

* **Bad**: `:param T:  Temperature values` (The reader has no idea how the temperatures are organized.)
* **Better**: `:param T: Matrix of temperature values` (At least now we know it's a matrix.)
* **Best**: `:param T: Matrix[ntime, ngrid] of temperature values, time steps in rows, grid cells in columns.` 
(This time we know not only that it's a matrix, but how it's organized.  We could readily create or use such a structure.)

### Document purpose of functions and function arguments
The purpose of any function/sub-routine should be documented along with a description of all arguments. Do this **before you write the function** so you are clear as to what you writing this function for!

* Describe any limits on valid values for arguments
* Describe use of any optional parameters



### Consider use of a documentation generator
Programs such as Doxygen, Javadoc, etc. extract comments to generate program documentation.

We've not consistently done this at JGCRI, but others have found this useful. 

Even if you don't actually use the documentation generator, the documentation formats can still be useful; e.g. in GCAM use of Doxygen style comments has resulted in consistent and more fully documented code, even though we have rarely actually used the system to generate separate documentation).

### Documentation should not be an afterthought
Write documentation before and as you code, not afterward. 

* Documenting as you go, and before you write code segments, relieves your working memory of having to store information. 
* Write down in comment lines what your current code block is going to accomplish, and what the next code block is going to do.

## <a name="bp-modular">5. Plan Ahead (Writing Modular code) </a>

As code gets longer, its harder to keep track of context and flow. A key technique for good coding is to break things into smaller functions. Instead of one long block of code, you want to aim for a short, high level program that calls functions. For example the following:

```
program create_fishing_projection()

  read_historical_data()

  read_projection_scenario()

  calculate_historical_trends()

  extend_trends_into_future()

  check_for_outliers()

  write_output()

```

is much clearer than a five-page function that does everything in one block.

Breaking up into modules requires thinking through how information is stored and passed between functions. Doing this will result in more reliable code.
 
### Plan ahead
One key to writing modular code is to plan ahead. There are different approaches to coding, but just like writing a paper, it is generally very useful to first write down an outline of what your code needs to do to help structure and organize your work. 

Central to most code is information flow, so drawing a diagram of what information needs to flow into and out of each function is a useful start. List the tasks to be undertaken within each function. If this gets too large or complicated, further modularization is likely needed. There are formal methods for doing this, such as [UML](https://en.wikipedia.org/wiki/Unified_Modeling_Language), although for smaller projects an informal diagram with notes is often sufficient. 

Once you've diagramed your code, talk it over with someone else. It doesn't have to be someone in your team (in fact, it's often better if it is not someone on your team).

### Follow the DRY Principle - "Don't Repeat Yourself"
Anything, either code or data, that occurs in more than one place is going to be more difficult to maintain and increases the chance of errors.

#### Data should appear only once
There should be only one authoritative representation of any data in the system.

 * Conversion values should be defined once and this value used throughout.
 * Coordinate and unit systems and other reference data should be clearly defined.

One common problem is embedding a number, such as a year or number of items, within a calculation or control statement, as was done in our [bad code example](#bp-bad-code-example) above. When you do this:

* If this number were to change in the future (such as the number of ocean basins in the example), someone would have to search through the code to change all instances of this. Instead this constant should be defined in a way that is available to all the code that needs this information.  
* Number with no context also make the code harder to understand.

A corollary to this is that if a (non-constant) data item is changed as a result of a calculation, make sure the code and data are structured in a way that this change will be consistently available wherever this data item will subsequently be needed throughout the system.
> If this is not the case, then a re-design is likely necessary.

#### Re-use functions instead of copying code
If you find yourself copying a chunk of code block from one place to another, this may be a good occasion to create a function that performs those operations.

* If the function is well-named this also means that the functionality can be remembered (and referenced) as one single chunk (less stress on working memory)
* This has the additional benefit that is is easier to check for argument validity and emit errors if bad values are found (see below).

#### Avoid for loops and similar constructions

Sometimes a loop is necessary, but in many higher level languages it's often not. If a loop is required, avoid hard coding limits, such as was done in our [bad code example](#bp-bad-code-example) above. Many languages allow you to identify the number of elements in a multi-dimensional variable, so use that capability. In C++, use iterators.

In a language such as R, functions such as `apply` and `lapply` are more efficient and powerful means of achieving what you would otherwise do in a loop. This requires you to break your operations into discrete functions, but after reading this document you now are going to do this anyway, right?

## <a name="bp-errors">6. Plan for Mistakes</a>

Your code will eventually have mistakes! A number of practices will help reduce mistakes and make it easier to debug.

> Modular programing is a first step! Breaking code into smaller chunks makes it easier to check that each piece is doing what its supposed to.

### Defensive programming: assertions.

Add assertions. Everywhere. Particularly in functions.

Assertions are checks that a chunk of code is getting values that are valid for the operations being performed. As you write code, ask yourself *under what conditions will this fail*, and then write assertions to check for those conditions. The classic example is when computing `1/x`, you assert that `x` must not be zero.

**For most scientific code, if a necessary condition is not met the code should error and stop. Otherwise you risk getting an invalid result.**

Some example conditions to check for:

* data exists
* value <> 0
* value > 0
* value is numeric
* value is within [range]

Also:

* take advantage of defensive programming features in the language

Many languages will have specific features that can be used to help avoid problems. Look at existing style guidelines for the language you are using for tips. 

For example (revisiting the example from section 3), if working in many languages you can declare a variable as a constant whose value cannot be changed:

`const NUM_OCEAN_BASINS = 5`

This provides both *syntactic* and *semantic* support for the fact that this is a constant and should not be messed with. 

Such checks should be common within functions that you write! 

Assertions can make up a sizeable fraction of the code in well-written applications, just as tools for calibrating scientific instruments can make up a sizeable fraction of the equipment in a lab [Wilson et al 2014](http://dx.doi.org/10.1371/journal.pbio.1001745).

In addition to helping avoid invalid results, assertions simplify debugging since you have a point to start from if an error occurs. 

They also serve as additional functional documentation, delineating the conditions under which a function is valid.

### Check for data consistency 
We often use code for data processing. Look for these opportunities to check that data is consistent as a calculation proceeds and put in assertions that check.

In many cases for physical quantities, data are additive, so as processing proceeds you can check that disaggreated data add up to totals.

* This can be particularly useful when reading and processing input data. You can sometimes check that your sum adds up (within some small tolerance) to the "total" line in the original data.

Many physical quantities should always be positive, so check that they remain so after manipulation.

Check outputs as well as inputs for consistency. 

### Write out diagnostics

For data processing, diagnostic messages and output files can be very valuable for debugging and code checking.

For example, have any categories been dropped or not processed?

*Log files* are an important diagnostic tool for processing codes.

*Write out diagnostic graphs* (line graphs for time series, scatter graphs for some data types). Can be useful for detecting discontinuities and outliers.

### Use unit testing 
When you design a function, consider if there is a known input/output relationship that can be used as a unit test. Most modern software systems have unit testing libraries that can be integrated into code. 

Even if you don't formally incorporate unit testing into your code, the concept is still useful for code testing. Use a simplified test case to check that your code produces a reasonable result. 

A variant on this for code that processes complex data is to trace through from input to output a straightforward example (e.g. a sector that has only one technology, etc.).

If you find a bug, turn that into a test case through use of unit testing or assertions.

### Plan for running your code multiple times
Even if just writing some code to crank out a graph or quick analysis, you will likely end up running that code more times than you initially think! So plan for that, put in a little extra time for good design (flexibility, documentation) and that will pay you back when you end up re-running that for the third, fourth, and fifth time.

Also, if you design your code flexibly, its more likely that it could be useful in your next project or analysis. 

Whatever you've generated with this code, remember that someone (such as **Future You** or a paper referee) may request that you re-run this later (with updated data, or some formatting change). So take the time to comment (and design) your code so that it will be easy to read and understand what was done.

## <a name="bp-collaborate">7. Collaborate </a>
### Always use code review.

Do this even if not working on a team. Getting someone else to comment on your code is incredibly valuable. 

Also, knowing someone else will look at your code (this month, not eventually) will encourage good programing techniques.

Don't be shy, **use code review!**

### Pair programing
>in which two developers sit together while writing code. One (the driver) actually writes the code; the other (the navigator) provides real- time feedback and is free to track larger issues of design and consistency. Several studies have found that pair programming improves productivity [Wilson et al. 2014](http://dx.doi.org/10.1371/journal.pbio.1001745)

Even if done only for a short time, or when tackling a tricky problem, this helps you "get out of your own head", and the overall effort can benefit from the interaction.

### Use Version Control

Use version control for any software project. Even if the repository is only on your machine.

Some of the many advantages of using version control, even if you are working on your own: helps you track changes, you can easily revert to a previous version, and allows you to create a branch of your project, where you can try out a new feature without mucking with your main code.

Another advantage of version control is that it facilitates collaboration, making it possible for multiple people to work on a project at one time. Many version control server systems (such as GitHub and JIRA) facilitate code reviews, making it easy for others to comment and for you to respond to those comments.

So use version control. Once you have that set up, your repository can easily be put online. (The online repository also serves as an off-site backup, but you already have automatic, off-site backup, right? If not set that up *right now*.)

Resources:

[Getting Started About Version Control](https://git-scm.com/book/en/v2/Getting-Started-About-Version-Control)

[Video: What is version control?](https://www.git-scm.com/video/what-is-version-control)

[Why should I use version control?](https://stackoverflow.com/questions/1408450/why-should-i-use-version-control)

## <a name="bp-reinvent">8. Don't Reinvent The Wheel </a>

Someone likely has encountered whatever it is you are trying to do. See if a solution is out there.

* Check for packages/libraries of the language you are using to see if someone has already solved this issue. 
 * In a language such as R, there are lots of solutions out there.
 *  However, don't necessarily use the first item you come up with in a google search! Not all solutions to a problem are created equal. 
 * Make sure the copyright status of something you find is comparable with the needs of your project. Avoid licenses with "flow through" requirements. These can be incompatible with the open-source licenses we use at JGCRI.
 *  Talk to colleagues. They can help direct you to potential solutions. (At JGCRI, for example, we've evolved over the past few years in terms of what packages we like to use. Some have proved to be more robust than others. The [GCAM Data System wiki](https://github.com/JGCRI/gcamdata/wiki) can be a useful resource. 
*   Re-use code from other projects.
 * If you modularize your code, particularly splitting actions into functions, this makes it easy to reuse that functionality in other projects. 
 * Remember, if a function starts getting to long, break it into smaller bits.
 
