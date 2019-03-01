# Chickpea

Chickpea is a Twine 2 story format based on [Snowman](https://github.com/videlais/snowman). It's meant to be used in the second semester of Coding for Filmmakers at BSA.

Like Snowman, Chickpea is a minimal Twine 2 story format designed for people who already know
JavaScript and CSS. It's designed to implement basic functionality for playing
Twine stories and then get out of your way.

Chickpea includes [jQuery](http://jquery.com) and [Underscore](http://underscorejs.org/) for you.

## Usage Documentation
### Passage Links
All double-bracketed links (`[[passage]]`, `[[displayed->passage]]`, and `[[passage<-displayed]]`) are converted to functional links.

### HTML
Passages may contain HTML. This includes `<a>`, `<div>`, `<span>`, `<script>`, `<style>`, `<img>`, `<video>`, `<audio>`, or any other valid HTML tag.

### JavaScript
JavaScript code can be included directly in passages. You can both execute JS statements or print the result of JS expressions in your passage. This functionality is supported using [underscore templating](https://underscorejs.org/#template)
#### Executing JS
You can execute JavaScript code in a passage by wrapping it in `<%` and `%>`.

For example, the following will log a random number whenever the passage it's included in is displayed:

```
<%
var randomNumber = Math.random();
console.log(randomNumber);
%>
```
#### Printing JS
You can print the result of a JavaScript expression to a passage by wrapping it in `<%=` and `%>`.

For example, the following will print a random number to a passage:
```
<b>Hi there! Your random number of the day is <i> <%= Math.random() %> </i>. Enjoy!</b>
```

The above will result in something that looks like:

<b>Hi there! Your random number of the day is <i>0.21317846410451302</i>. Enjoy!</b>

#### `story`: object
The story object (accessible via JavaScript at `window.story` or simply `story`) is one of the global JavaScript variables created by Chickpea. It provides access to information about the current story through the properties and functions listed below. 

Note: This is an incomplete list of `story` properties and functions. More detail is located at the the [snowman docs](https://twinery.org/wiki/snowman:window-story), from which much of these docs were taken.

##### `story.state`: object
An object that stores data that persists across a single user session. Any other variables will not survive the user pressing back or forward. JavaScript in passages can also access `story.state` as `s`.

`story.state` can be used to do many things. A non-exhaustive list of things you can do with `story.state`:

###### add a property to it when the player accesses a certain passage
For example, if the player clicks the "pickup the flute" passage link, you may take them to a passage containing the JavaScript `<% story.state.playerHasFlute = true; %>` along with narrative writing detailing that they picked up the flute. The `story.state.playerHasFlute` property now represents that the player has acquired the flute item. On a future passage, perhaps the player comes across a venomous cobra. You want to allow the player to charm the cobra with their flute; however, what if the player never acquired the flute? One option is to use an `if` condition to only show the "charm snake with flute" passage link if `story.state.playerHasFlute == true`. That code may look something like the following:
```
<% if(story.state.playerHasFlute == true) %>
[[charm snake with flute]]
<% } %>
```

###### keep track of a value that changes frequently, like the player's score
Perhaps you wish to score players based on their choices. If the player goes to a good passage, their score increases by 100. If the player goes to a bad passage, their score decreases by 100. 

To do this, on the first passage you should initialize the score to some value (in this case, let's say 0):

`<% story.state.score = 0; %>`

If the player goes to a good passage, we increase the score by 100:

`<% story.state.score += 100; %>`

And if the player goes to a bad passasge, we decrease the score by 100:

`<% story.state.score -= 100; %>`

The final passage in the story should give the player their score. This can be done by printing the score variable:

`Your final score is: <%= story.state.score %>`

which, if the player's score is 400, would be rendered as:

__`Your final score is 400`__

#### `story.render`: function(id)
id: string | number - the ID or name of the passage you wish to render

return type: string

Returns the HTML source for a passage. This is most often used when embedding one passage inside another. In this instance, make sure to use `<%=` `%>` instead of `<%-` `%>` to avoid incorrectly encoding HTML entities.

#### `story.show`: function(id)
id: string | number - the ID or name of the passage you wish to show

return type: undefined

Displays a passage on the page, replacing the current one. If there is no passage by the name or ID passed, an exception is raised. 
