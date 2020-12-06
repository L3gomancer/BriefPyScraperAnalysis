# An Analysis Attempt
From [furiousthinking](http://cian.furiousthinking.org/code/dark/) blog\
Forgive me redefining the wheel but Im just making sure everybody's on the same page

We know the Youtube game consists of videos with URLs (and their IDs) that point to other URLs. So the Dot results should eventually look like:
```dot
digraph G {
M3BM9TB [label="A Dark Room"]             // label a node
39lCli22dA [label="Northern room"]        // label a node
M3BM9TB -> 39lCli22dA [label="Go North"]  // connect nodes and label the connection
}
```

The raw XML holds the target URL IDs and probably the video's title. It is (somehow) converted to JSON like the sample below:
```json
{
  "hvkjP6dqpfY" : { "name" : "The Dark Room",
                    "link" : { "GO NORTH" : "X6PD4IUlCwo",
                               "TURN ON LIGHT SWITCH" : "-WG36-XrcU4",
                               "SLEEP" : "lsteokF2w8Q",
                               "WHY?" : "PPifzjxTahY" } },
```

JSON is made of pairs of keys and values separated by a colon, a value can be another nested object with more pairs. Note the whole file has a parent object (first curly bracket), then each 'video' object starting with the current URL ID as key, it contains two keys "name" and "link". The value of "name" is the video title, the value of "link" is another object with 4 items, the annotation's text label and then the annotation's URL IDs. (I could be wrong)\
This is then converted into Dot syntax. I don't understand Python but here's my attempt to analyse the script from the blog. \
First it imports a `json` module to use the methods and assigns the JSON object to an object variable "graph". \
It seems the `print` statement borrows syntax from C++. It groups references in parenthesis after the single `%` separated by commas, then before the `%` it has a string in single quotes with format specifiers `"%s"` and their occurences correspond to the positions of the references.

```python
# A for-loop cycles through every 'video'. The method ".keys()" grabs the keys and assigns to the variable "key"
for key in graph.keys():

# "key" becomes the first layer key which is the current video's ID
# "graph[key]["name"]" becomes the value of the "name" key in the second layer
  print '"%s" [label="%s"]' % (key, graph[key]["name"])

# Now a nested for-loop which inherits the "key" variable for printing again.
# This time it targets the value of the "link" key in the second layer, grabs all values. It and declares two more variables "label" and "link_key"
  for label, link_key in graph[key]["link"].items():

# 1st "%s" is assigned var "key" again (which is the ID)
# 2nd "%s" updates to var "link_key" (which is the annotation's ID)
# 3rd "%s" updates to var "label" (which is the annotation's text. Comes from the inner json key)
    print '"%s" -> "%s" [label="%s"]' % (key, link_key, label)
# I think the var "link_key" shouldve been called "link_value" since it becomes the json value
```
