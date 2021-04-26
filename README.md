# WikipediaRelations
Python script for finding quasi-incestuous relationships according to Wikipedia

Basically: choose a person who's available on wikipedia. Then use this program to get a dot diagraph of their family relationships, and where they fall in any lines of succession for a title (eg, royal crown).



<hr>



### Example: Relationships of the Ancient Egypt Ptolemies

_Red dashed line is marriage. Gold arrow shows succession of a title. Black arrow is parent/child. Click for bigger image._

![Ptolemy diagram](./documentation_imgs/Ptolemies.png)



The graph is a bit messy, as auto generated ones usually are. But zooming in and tracing out the lines yields up a variety of interesting characters and complicated relationships. My favourite is [Cleopatra Selene of Syria](https://en.wikipedia.org/wiki/Cleopatra_Selene_of_Syria). This woman inherited four different titles, largely due to her five marriages:

- *Ptolemy IX:* her oldest brother.
- *Ptolemy X:* her other brother.
- *Antiochus VIII:* her maternal cousin, the widower of her sister Tryphaena.
- *Antiochus IX:* her maternal cousin, the widower of her sister Cleopatra IV, and the brother of her previous husband Antiochus VIII.
- *Antiochus X:* her step-son, who also happened to be her nephew.



![Ptolemy diagram](./documentation_imgs/CleopatraSelene.png)





<hr>



### Running the Code

Clone the repo, or just download the `WikipediaRelations.ipynb` Jupyter notebook. Then you can either run your commands at the bottom, or import it into a new file. 

> Uses Python3. Dependencies: [graphviz](https://graphviz.readthedocs.io/en/stable/manual.html), [BeautifulSoup](https://pypi.org/project/beautifulsoup4/) and [palettable](https://jiffyclub.github.io/palettable/).

**Basic usage:**

```python
dot = wiki_relationship_diagraph("Ptolemy XI", "Ptolemy_XI_Alexander_II", count=200)
```
Returns (and saves) the dot graph for the Ptolemies given above. See it in more detail [here](https://raw.githubusercontent.com/HebeHH/WikipediaRelations/main/Ptolemies.png).




**Full function and with default paramaters:**
```python
wiki_relationship_diagraph(basis_person_name, basis_person_link, count=150,
                               name="WikiRelations", trim=True, save=True, save_format=False)
```

These paramaters are:
- `basis_person_name`: Display name of starter person
- `basis_person_link`: The last part of the wikipedia link for that person (eg: the link `https://en.wikipedia.org/wiki/Demetrius_I_of_Bactria` becomes `Demetrius_I_of_Bactria`.
- `count`: How many related people to try and pull
- `name`: Name to save the dot graph as
- `trim`: Recursively remove all 'dead ends' that don't go much of anywhere (I was interested in finding cyclic connections)
- `save`: Whether to save the graph to disk or not.
- `save_format`: Format to save the graph as. `False` means it will use your computer's default value. Otherwise, give a format value as a string from this list of what's available: https://www.graphviz.org/doc/info/output.html



<hr>




### How it works

**1.** _Scrape Wikipedia page_

After getting the Wikipedia HTML for the given person, use BeautifulSoup to localize the `infobox` section. You're probably already familiar with this - see the one for Obama below.

![Wikipedia infobox](http://thinking.is.ed.ac.uk/wiki-basics/wp-content/uploads/sites/19/2017/12/Obama-infobox.png)

**2.** _Find relevant lines_

Go through the infobox table and pull out all lines where the title matches one of the keywords. Eg: `Succeeded by`.

**3.** _Add new people and relationships_

For each relevant line, find any links in the value. This doesn't pay much attention to the actual text; it just looks for links (`<a>` tags) and gets the reference. These links represent a new `Person`, and are added to the queue. The relationship information is added to the current `Person` object; that they have _this relationship_ (from keyword) to _this person_ (from link).

**4.** _Repeat_

Go through the above steps for all the new related people who's links have been found. This isn't done infinitely, it's only done for `count` number of people.

**5.** _Create graph_

At this point there's a list of _People_ objects, each containing their own relationships. This is transformed into a nodelist/edgelist graph structure. 

**6.** _Prune_

Remove 'unimportant' people from the graph, ie: people on the fringes without connections. This prunes all people with only one connection, and then recurses, until only highly-connected people are left. This step is optional: you can turn it off by setting `trim=False`.

**7.** _Draw graphic_

Use the [graphviz](https://graphviz.readthedocs.io/en/stable/manual.html) library to create the diagraph representation and save it.



<hr>




### Important Notes

Be aware:
- Will take awhile as we're scraping each wikipedia page individually. No parallelism yet.
- There's no guarantee that the program will actually find the interesting relationships.
- All titles are represented the same way (with the gold arrow). There's usually several different titles represented on the same graph, because titles are nepotistic like that. You'll need to google to work it out.

Known bugs:
- The relation shown is always with the _link_ given in the Wikipedia section, not the name. Sometimes this doesn't match. Eg: the spouse section on [Demetrius I of Bactria's](https://en.wikipedia.org/wiki/Demetrius_I_of_Bactria) Wikipedia page links to a secondary person (eg: "Daughter of Daughter of Daughter of [Antiochus III](https://en.wikipedia.org/wiki/Antiochus_III_the_Great). In the graph above, you can see it's showing a marriage link between Demetius and Antiochus instead.
