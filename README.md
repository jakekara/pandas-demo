<style>
.best-practices{
padding: 10px;
background: LightSteelBlue;
margin-bottom:20px;
margin-top:20px;
max-width: 650px;
}
.quote{
border-top: 1px solid gray;
border-bottom: 2px solid gray;
}
pre {
max-width: 650px
}</style>

Trend CT and most of the data journalism world use code instead of visual spreadsheet applications to clean up and interrogate data. 

In this walkthrough we'll use the Python programming language to find the biggest recipients of vendor payments from the Connecticut Airport Authority in a format readable by anyone, not just people who know how to write code, called a "notebook."

<div style="margin-right: 10px; float: left;"><script class="chapterScript" src="http://projects.ctmirror.org/tools/chapters/init.js" type="text/javascript"></script></div>
The main drawback with visual tools is that while they're intuitive, they don't provide a mechanism to show your work step-by-step. For journalists, this is a problem. When we interrogate data, just like when we interrogate — err, "interview" — a source, we need to take notes (see Appendix A: Philosophical Musings for why we think this is important).

Instead of using the visual methods of manipulating tables in a program like Excel or Google Sheets, we'll perform the same types of operations with code. The tools we'll use are the Python programming language, a Python library for statistical analysis called Pandas, and a notebook program called Jupyter. The end product will look like this:

<div><img src="http://trendct.org/files/2016/07/Screen-Shot-2016-07-20-at-3.14.07-PM.png" alt="Screen Shot 2016-07-20 at 3.14.07 PM"/></div>

Another environment for analyzing data that some people, such as my colleague Andrew Ba Tran, prefer is the <a href="http://trendct.org/2015/06/12/r-for-beginners-how-to-transition-from-excel-to-r/">R statistical programming language</a> with R studio. Another method is to use a database and write SQL code for your analysis.

One of the key features that makes all of these tools sufficient for data analysis is <a href="https://en.wikipedia.org/wiki/Relational_algebra">relational algebra operations</a>. A relation is a fancy word for a table. Relational operations can take a table as input and return a table as output. For example, we might have a relation (read: table) called census with two attributes (fancy word for columns): names of states and estimated population. To narrow down the relation to the tuples (rows) with a population greater than 5 million, we'd make a new, relation using a <a href="https://en.wikipedia.org/wiki/Selection_(relational_algebra)">selection</a> operation. A selection can be written with Greek symbols, but my Greek is rusty, so I'll use SQL, which you can probably decipher even if you've never read a line of code:

<pre>
SELECT state, population
FROM census
WHERE population > 5000000
</pre>

The exact same expression can be written in R or Pandas. These tools also offer a suite of statistical functions that, combined with relational algebra, allow for powerful transformation and analysis. 

Don't sweat which tools you choose. I recommend learning a the basics of each to see what you like (See Appendix B: Why Python for my thoughts on why I use Python).

<h5>Difficulty level</h5>

Easy-ish, but if you've never written any code before, you'll need to spend time on the resources linked in the article. 

This isn't meant to be an exhaustive introduction to any of the technologies used, but a look at how they can be used in concert with a real-world example. For a more comprehensive guide, I recommend <a href="http://pandas.pydata.org/pandas-docs/version/0.18.1/tutorials.html#pandas-cookbook">the Pandas Cookbook</a>. There are more resources on Python in Appendix C of this guide.

<h5>Software to install</h5>

For this guide, you'll need to <a href="http://docs.python-guide.org/en/latest/starting/installation/">install Python</a>, a python library called <a href="http://pandas.pydata.org/pandas-docs/stable/install.html">Pandas</a>, and an interactive programming environment for data scientists <a href="http://jupyter.readthedocs.io/en/latest/install.html">called Jupyter</a>. (If you have a Mac, Python comes in pre-installed.)

I haven't used it myself, but <a href="https://docs.continuum.io/anaconda/pkg-docs">Anaconda</a> is a popular package that combines a lot of <a href="https://en.wikipedia.org/wiki/Free_software">free</a> software for data science in Python (and R), including Pandas and Jupyter.

<h5>Setting up the environment</h5>
Before we can start analyzing data, we need some data to analyze and a folder to work in. All the folders, data and code for this tutorial can be found this GitHub repository, (so you can either download the whole thing to your Desktop and skip to step 4).

1. Make a new folder on your Desktop and call it pandas-tutorial. That's your project folder.

2. Open your project folder and make a new folder inside called "data". The data folder is where you'll keep your raw source data. 

<div class="best-practices"><strong>Best practices: </strong> I always put the raw data in a data subdirectory, and I don't alter the file directly. This way anyone auditing my code can obtain the data from the source themselves and reproduce my analysis. I try to never "pre-process" raw data in some other program, like Excel.</div>

3. Download <!-- LINK --> this csv from the repo for this tutorial on GitHub and put it in your data folder.

4. On a Mac, open up Terminal. It can be found in the /Applications/Utilities folder or in Spotlight (Command+Space) by searching for "Terminal." 

If you've never used Terminal, check out this <a href="http://www.imore.com/how-use-terminal-mac-when-you-have-no-idea-where-start">iMore article.</a>

On a Windows computer, this process will be different. This guide should help you get familiar with the <a href="http://www.cs.princeton.edu/courses/archive/spr05/cos126/cmd-prompt.html">Windows command line</a>.

5. Enter the following command ($ represents the command prompt, where you type a line and then hit the return key. Don't type the dollar sign. ).
<pre>$ cd ~/Desktop/pandas-tutorial</pre>
To make sure you're in the current directory, enter the following command:
<pre>$ pwd</pre>
It should print out the following output:
<pre>/Users/jakekara/Desktop/pandas-tutorial</pre>
Except, instead of "jakekara" it will have your username.

6. Launch Jupyter Notebook:
<pre>$ jupyter notebook</pre>
You'll see a bunch of text in the terminal window that you can safely ignore before a new browser window appears that looks like this:

<div><img class="aligncenter size-medium wp-image-15609" src="http://trendct.org/files/2016/07/jupyter-home-screen.png" alt="jupyter home screen"/></div>
<h5>Creating a notebook</h5>
In this page, you can see the data directory. To launch a notebook:

1. Click thew "New" button on the right side of the page and select Python 2 (or Python 3. It just depends which version of python you have installed).

<div><img class="aligncenter size-full wp-image-15610" src="http://trendct.org/files/2016/07/jupyter-new-notebook-button.png" alt="jupyter new notebook button"/></div>

2. In the new page that opens up, give it a name by clicking the text area that says "Untitled" and entering a name, like "Intro to Pandas."

3. Click in the first "cell" and then click the dropdown that says "code" and change it to "Markdown."

Markdown is a shorthand that gets converted to HTML. In markdown fields, like the one we just created, we can put our annotations. Headlines are preceded with # or ## or ###. The more #'s the smaller they appear. For more on markdown syntax, <a href="https://daringfireball.net/projects/markdown/syntax">read this</a>.

4. Click in the markdown field, enter a headline and explanation of the project. Then hit shift+enter to render the cell into HTML and jump to the next cell (in this case, a new cell is created. The default cell type is "code").

<div style="text-align:center;"><img src="http://trendct.org/files/2016/07/markdown.gif" alt="markdown"/></div>

Let's add a field for code:

5. Click in the newly created cell and type print "HELLO, WORLD!"

6. Hit shift+enter to execute the code (same as the shortcut to render the Markdown cell).

Unlike when the markdown cell rendered, the code cell doesn't change in appearance. Instead the output from the code appears below, if there is any. In this case, the output is the string "HELLO, WORLD!"

<div class="best-practices"><strong>Best practices:</strong> Don't skimp on the markdown cells. Use them throughout your code to explain what topics you're going to explore, and what questions you want to answer. Non-technical readers should be able to read just the markdown sections of your notebook and get a better understanding of how you analyzed the data.</div>

<h5>Tinkering with some real data</h5>

This section moves quickly. Describing how to write code in Python is beyond the scope of this tutorial, so if you're confused about any of the Python code in this section, you should read any of these great resources on Python or watch the lectures from this MIT computer science course using Python for free on EdX.

1. Import the pandas library with a commonly used alias "pd" by putting the following in a new code cell and executing it (remember, shift+enter):

<pre>import pandas as pd</pre>

2. Next, we'll import the spreadsheet to a data frame. We import data using pandas' <a href="http://pandas.pydata.org/pandas-docs/stable/io.html">"read_" methods</a>. In this case, we'll use read_csv:

<pre>caa_data = pd.read_csv("data/Connecticut_Airport_Authority_Financial_Data_FY_15.csv")</pre>

<div><img src="http://trendct.org/files/2016/07/Screen-Shot-2016-07-19-at-2.48.25-PM.png" alt="Screen Shot 2016-07-19 at 2.48.25 PM.png" /></div>

Now the dataframe is stored in the variable caa_data. [A dataframe is a python object that you can think of as a table, like a single tab of a spreadsheet. R uses the same terminology.

3. Use the <a href="http://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.head.html">.head()</a> method on the dataframe to see the first few rows:

<pre>caa_data.head()</pre>

<pre><img src="http://trendct.org/files/2016/07/Screen-Shot-2016-07-19-at-2.51.55-PM.png" alt="Screen Shot 2016-07-19 at 2.51.55 PM.png" /></pre>

The .read_csv() method worked well. Because of the structure of the spreadsheet, it was able to infer the column headings. By default, the read_ methods use the first row for column headings, but you can specify a certain number of rows to skip, if the spreadsheet isn't formatted as nicely as this one.

4. To select just the values in the "Amount" column, we can use the following syntax:

<pre>caa_data["Amount"]</pre>

Notice there are too many rows to display, so in the middle of the output there is a row "..." indicating that rows were skipped. For more on selecting data, <a href="http://pandas.pydata.org/pandas-docs/stable/indexing.html">read this</a>.

5. To select just rows where the category is "Office Supplies" we can use:

<pre>caa_data[caa_data["Expense Category"] == "Office Supplies"]</pre>

6. The results of queries like the one above are also dataframes, and they can be stored to variables as well, so we can make a new variable for a table just for payments to office supply vendors.

<h5>Seeking useful information</h5>

Now we're ready to do something more useful. From this dataset we should be able to calculate the total dollar amount of all the payments combined, and also the total dollar amount for the 10 biggest expense categories. Start by stating what you want to find out before you start your analysis, with a new markdown cell.

1. Use the .sum() function on the "Amount" column:

<pre>caa_data["Amount"].sum()</pre>

<div><img src="http://trendct.org/files/2016/07/Screen-Shot-2016-07-19-at-3.09.39-PM.png" alt="Screen Shot 2016-07-19 at 3.09.39 PM.png" /></div>

Well, that didn't work! When Pandas imported the spreadsheet, it tried to infer what type of data was in each column. It didn't recgonize the "$" signs in the "Amount" column as numbers, so instead it treated them as strings of text. Calling the .sum() function on strings just concatenates them together.

We first have to convert the values in the "Amount" column to numeric values. Here's some code that will do that, and saves it in a new column called "Amount_numeric":

<pre>import re
caa_data["Amount_numeric"] = caa_data["Amount"].apply(lambda x: \
                         re.match(r"\$(?P<amount>([-0-9\.]+))",x)\
                        .group("amount"))</pre>

I won't get into the details of how this code works -- I'll save that for a later article that talks specifically about strategies for cleaning up data. But if you're interested in unraveling it you'll want to read up on <a href="http://www.secnetix.de/olli/Python/lambda_functions.hawk">lambda functions in Python</a> and <a href="https://docs.python.org/2/library/re.html">regular expressions in Python</a>.

2. Now, try that .sum() method again.

<div><img src="http://trendct.org/files/2016/07/Screen-Shot-2016-07-19-at-3.34.20-PM.png" alt="Screen Shot 2016-07-19 at 3.34.20 PM"/></div>

Huzzah! Now let's group the data by Expense category and get the sum of the payments for each group, limiting results to the top 10 results in descending order.

<pre>
# We can use the method .agg method to get summary data 
# from the grouped object. We'll need to import another library,
# called numpy to do it. Read more on this groupby method here:
# http://pandas.pydata.org/pandas-docs/stable/groupby.html#applying-multiple-functions-at-once
import numpy as np
caa_data.groupby("Expense Category").agg({"Amount_numeric":sum})\
.reset_index()\
.sort_values(by="Amount_numeric", ascending=False)\
.head(10)</pre>

<div><img src="http://trendct.org/files/2016/07/Screen-Shot-2016-07-19-at-3.24.47-PM.png" alt="Screen Shot 2016-07-19 at 3.24.47 PM" /></div>

<h5>Further reading</h5>

You should now have a sense of the environment in which you can analyze data in a reproducible, publishable way. You'll want to dig deep into Python and Pandas as you go forward. Coming up, I'll describe some techniques I've learned for turning text into useful data.

<hr>

<h5>Appendix A: Philosophical musings</h5>

Data journalism looks different in every newsroom but most of it fits into two categories: data analysis and data visualization. 

Visualization means illustrating data with charts, graphics and interactive content. 

Analysis includes obtaining, cleaning up and "interrogating" the data. But isn't that just called "journalism?" Yes. Data isn't just a beat, like tech or business journalism but a practice virtually every journalist is engaged in.

<h5>Guiding principles</h5>

These principles are up for debate, but we at Trend CT feel any journalist engaging in the practice of data journalism, regardless of whether their title includes the word "data" and regardless of what tools he or she is using.

1. <strong>Accountability</strong>. When we present numbers we calculate ourselves, we should also publish the steps we did to get there from source data, like an excel file from the FEC.

2. <strong>Openness</strong>. We believe in friendly, productive competition, and we understand the initial reluctance to sharing your work. What if the competition uses it to publish the same story and steal your thunder? Well, first of all, that happens with every kind of story, data or not, and they could lift (or "aggregate") without even seeing our methodology. The upside of sharing is that other news outlets or policymakers or just interested readers can check our work. They might find a mistake, which we can correct. The other big upside is that they can take our work and build on it, better serving readers. We want to see good journalism, even if we didn't produce it.

3. <strong>Reproducibility</strong>. This isn't just an ethical thing or an accuracy thing. When you analyze data, you should do it in a way that you can reproduce so that the next time a new version of the data comes out, you can just "hit go" and not have to redo all your work. Time is a precious resource for newsrooms. Think about future-you. Don't waste his or her time.

<hr>

<h5>Appendix B: Why Python</h5>

I use Python because I don't just do data analysis. I write web applications and other tools in Python. I've been writing Python code long before I got started in Data journalism. I'm comfortable with Python and I'm able to use code I write for data analysis in applications I write, and vice versa. In addition to Pandas, the Agate library, a well-designed data analysis library specifically geared for journalists, is a Python package.

Where Python is a general purpose language, R is finely tuned for data science.

<h5>Appendix C: Further reading</h5>

If you've never done any <strong> Python programming</strong> at all, you'll want to start with one or more of these guides:

* https://www.python.org/about/gettingstarted/
* https://developers.google.com/edu/python/introduction
* http://docs.python-guide.org/en/latest/
* https://docs.python.org/3/tutorial/introduction.html

These guides are interactive:
* http://www.learnpython.org/
* https://www.codecademy.com/learn/python

Or this excellent computer science introduction course from MIT available to watch for free on EdX: 
* https://www.edx.org/course/introduction-computer-science-mitx-6-00-1x-8

For more on <strong>Pandas</strong>, check out this compilation of tutorials, including this very thorough "Pandas Cookbook":

http://pandas.pydata.org/pandas-docs/version/0.18.1/tutorials.html#pandas-cookbook

<h5>Other tools for data journalists</h5>

For an alternative to Pandas, <strong>Agate</strong> is a well-designed data analysis library for journalists maintained by a good programmer and journalist<a href="https://github.com/onyxfish">Christopher Groskopf</a>:

* http://agate.readthedocs.io/en/1.4.0/

You can also use <strong>database applications</strong> to store and analyze data. This guide by AP data journalist Troy Thibodeaux walks through using SQLite, a server-less database:

* https://github.com/tthibo/SQL-Tutorial