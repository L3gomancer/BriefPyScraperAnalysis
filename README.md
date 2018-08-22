# An Analysis Attempt
From [furiousthinking](http://cian.furiousthinking.org/code/dark/) blog\
Forgive me redefining the wheel but Im just making sure everybody’s on the same page.\
  
We know the Youtube game consists of videos with URLs (and their IDs) that point to other URLs. So the Dot results should eventually look like:
```dot
digraph G {
M3BM9TB [label=”A Dark Room”]             // label a node
39lCli22dA [label=”Northern room”]        // label a node
M3BM9TB -> 39lCli22dA [label=”Go North”]  // connect nodes and label the connection
}
```

The raw XML holds the target URL IDs and probably the video’s title. It is (somehow) converted to JSON like the sample below:
```json
{
  "hvkjP6dqpfY" : { "name" : "The Dark Room",
    "link" : { "GO NORTH" : "X6PD4IUlCwo",
      "TURN ON LIGHT SWITCH" : "-WG36-XrcU4",
      "SLEEP" : "lsteokF2w8Q",
      "WHY?" : "PPifzjxTahY" } },
```

JSON is made of pairs of keys and values separated by a colon, a value can be another nested object with more pairs. Note the whole file has a parent object (first curly bracket), then each ‘video’ object starting with the current URL ID as key, containing key “name” and then it’s value is the video title, then another object with the annotation’s text label and then the annotation’s URL IDs. (I could be wrong)\
This is then converted into Dot syntax. I don’t understand Python but here’s my attempt to analyse the Python form the blog:
```python
# First it seems to import a ‘json’ module to use the methods
# a for loop cycles through every ‘video’ and the method “.keys()” grabs the ID and assigns to variable “key”
for key in graph.keys():

# The print statement uses “%s” variable which should be ID
# but then uses it again so it mustv updated to be the label “The Dark Room”
# the 2nd part calls the “key” var above, but then gets the value of the child JSON key
  print '"%s" [label="%s"]' % (key, graph[key]["name"])

# more complex AND in 3 parts. First for loop declares 2 variables. It assigns the 
  for label, link_key in graph[key]["link"].items():

# “%s” is assigned var “key” again (which is the ID)
# next “%s” updates to var “link_key” (which is the annotation’s ID)
# 3rd “%s” updates to var “label” (which is the annotation’s text. Comes from the inner json key)
    print '"%s" -> "%s" [label="%s"]' % (key, link_key, label)
# I think the var “link_key” shouldve been called “link_value” since it becomes the json value
```
