---
layout: post
title: "Getting to know MatPlotLib and Python-docx"
date: 2019-10-05
---

Recently, my boss asked me if I'd be able to automate and expediate the analysis of a set of data
so that my colleagues would be able to avoid manually copying and pasting numbers into 
columns on excel. For me this proposed a few new challeneges I had not yet encountered:
1. Writing a working piece of code in python, a language I'd used sparingly and never 
for any kind of big project up until now.
2. Figuring out how to generate graphs using matplotlib
3. Figuring out how to output everything as a word document containing images that depended on the raw
input data. 

Essentially, the task was this. How do I take this raw data that represents x, y, z, execute certain
transformations on the the numbers to get adjusted values, turn the results into a series of graphs, 
and then output these graphs as a word document along with a set of images that varied depending on 
all the results? 
It seemed simple enough and doable on python. But I ended up learning a bunch of new things I thought
I would throw into a little post for future reference. 

## Transformations

Honestly, the maths was probably the easiest part. I had excel formulas and all I had to do was apply
them to my data. I imported my data using pandas and shoved everything into an array:
```
import pandas as pd
data = pd.read_csv ('Raw data.csv')
dataset = data.values
```
I mostly used while loops to iterate through everything and apply the right changes. Once I had the final
numbers, I threw them into a new array and rounded them up into integers.

## Graphs

```
from matplotlib import pyplot as plt
from math import pi
```
I wanted 2 plots, a radar chart and horizonal bar charts. 
The radar was created using [this link](https://python-graph-gallery.com/390-basic-radar-chart/):
```
df = pd.DataFrame({
'group': ['A'],
'Comp' : [finals[0]],
'Pos': [finals[1]],
'EmC':[finals[2]],
'Soc':[finals[3]],
'Ene':[finals[4]],
'As': [finals[5]],
'Co':[finals[6]],
'Tr':[finals[7]],
'Re':[finals[8]],
'Ac':[finals[9]],
'Res':[finals[10]],
'Pro':[finals[11]],
'Tho':[finals[12]],
})

categories=list(df)[1:]
N = len(categories)
values=df.loc[0].drop('group').values.flatten().tolist()
values += values[:1]
angles = [n / float(N) * 2 * pi for n in range(N)]
angles += angles[:1]
ax = plt.subplot(111, polar=True)
plt.xticks(angles[:-1], categories, color='black', size=8)
ax.set_rlabel_position(0)
plt.yticks([10,20,30,40,50,60,70,80,90,100], ["10","20","30","40","50","60","70","80","90"], color="grey", size=7)
plt.ylim(0,100)
ax.plot(angles, values, linewidth=1, linestyle='dotted')
ax.fill(angles, values, 'b', alpha=0.3)
```

The bar charts were made using matplotlibs references:
```
import numpy as np
np.random.seed(1234)
plt.rcdefaults()
fig, ax = plt.subplots()

titles = ('Comp', 'Pos', 'EmC')
y_pos = np.arange(len(titles))
result = (finals[0], finals[1], finals[2])

ax.barh(y_pos, result, color = 'indianred', height = 0.5)
ax.set_yticks(y_pos)
ax.set_yticklabels(titles)

# this line adds labels to the ends of the graph
for i, v in enumerate(result):
    ax.text(v + 1, i, str(v) + str('%'), color = 'black', fontweight = 'bold')

plt.savefig('EI.png',  bbox_inches='tight')
```
I wanted to save the graphs to be able to use them in my word doc later. I found that if I didn't
add 'bbox_inches='tight', my saved png would be cropped and I would lose some information

![Bar chart](/assets/img/Bar Chart eg.png)

## Python-docx

Firstly, I had to install python-docx and set up what I'd need:
```
pip install python-docx
from docx import Document
from docx.shared import Inches
from docx.shared import Pt
```
Python-docx essentially writes information to a word document as indicated.
```
document= Document()
document.add_heading("My results!")
```
I didn't like the default font so I decided to change it to Arial:
```
font = document. styles['Normal'].font
font.name = 'Arial'
font.size =Pt(10)
```
And then I just started writing my saved graphs to the document:
```
p = document.add_paragraph("EI \n")
p.style = document.styles['Normal']
r1 = p.add_run()
r1.add_picture('EI.png', width = Inches(4))

document.save("MA.docx") # At the end of your code to generate the docx file
```
The next set of images I wanted to add were dependent on the final results. Depending on whether my numbers fell
between 0 - 30, 31 - 60 and 61 - 100, a different set of images would have to be generated.
For this, I just used a simple if elif statement to determine which of the images to load using
```
r1.add_picutre('IMAGE.PNG', width = Inches (6))
```
And once I'd done that I had a word document filled with information about the raw data that I'd inputed.
It was pretty cool to see that my code was able to take a raw set of numbers and spit out 
useful information and graphs. I learnt how to output word documents, a skill I think will come in 
pretty handy in the future, and began to learn the basics of matplotlib, which is always useful. 
