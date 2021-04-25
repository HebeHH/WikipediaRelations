# WikipediaRelations
Python script for finding quasi-incestuous relationships according to Wikipedia


Basically: choose a person who's available on wikipedia. Then use this program to get a dot diagraph of their family relationships, and where they fall in any lines of succession for a title (eg, royal crown).

Example:
```python
dot = wiki_relationship_diagraph("Ptolemy XI", "Ptolemy_XI_Alexander_II", count=200)
```
Returns (and saves) the dot graph below. See it in more detail [here](https://raw.githubusercontent.com/HebeHH/WikipediaRelations/main/Ptolemies.png).

_Red dashed line is marriage. Gold arrow shows succession of a title. Black arrow is parent/child._

![Ptolemy diagram](https://raw.githubusercontent.com/HebeHH/WikipediaRelations/main/Ptolemies.png)


Full function with default paramaters:
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


Notes:
- Will take awhile as we're scraping each wikipedia page individually. No parallelism yet.
- There's no guarantee that the program will actually find the interesting relationships.
- All titles are represented the same way (with the gold arrow). There's usually several different titles represented on the same graph, because titles are nepotistic like that. You'll need to google to work it out.

Known bugs:
- The relation shown is always with the _link_ given in the Wikipedia section, not the name. Sometimes this doesn't match. Eg: the spouse section on [Demetrius I of Bactria's](https://en.wikipedia.org/wiki/Demetrius_I_of_Bactria) Wikipedia page links to a secondary person (eg: "Daughter of Daughter of Daughter of [Antiochus III](https://en.wikipedia.org/wiki/Antiochus_III_the_Great). In the graph above, you can see it's showing a marriage link between Demetius and Antiochus instead.
