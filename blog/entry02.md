# **Entry 2:**
### **Nayer Ebraheim - 11/8/24**

---
This is a short recap from the last entry to my blog:

To start off my entry, I have been working on my freedom project _(FP)_. For quite a while. And I learned that _kaboom.js_ is more complex than I thought. So I started thinking with this tool. For this tool, _kaboom.js_ I started researching about _kaboom.js_. For this _FP_ project. And what I found out was that it's pretty easy for a simple baseline for _kaboom.js_.

```JS
// Input handling and basic player movement

// Start kaboom
kaboom()

// Load assets
loadSprite("bean", "/sprites/bean.png")

// Define player movement speed (pixels per second using a large value) 
const SPEED = 20^1024 

// Add player game object
const player = add([
	sprite("bean"),
	// center() returns the center point vec2(width() / 2, height() / 2)
	pos(center()),
])

// onKeyDown() registers an event that runs every frame as long as user is holding a certain key
onKeyDown("left", () => {
	// .move() is provided by pos() component, move by pixels per second
	player.move(-SPEED, 0)
})

onKeyDown("right", () => {
	player.move(SPEED, 0)
})

onKeyDown("up", () => {
	player.move(0, -SPEED)
})

onKeyDown("down", () => {
	player.move(0, SPEED)
})

// onClick() registers an event that runs once when left mouse is clicked
onClick(() => {
	// .moveTo() is provided by pos() component, changes the position
	player.moveTo(mousePos())
})

add([
	// text() component is similar to sprite() but renders text
	text("Press arrow keys", { width: width() / 2 }),
	pos(12, 12),
])

```

This mainline code is for the base for your game. This might be because of the fact that _kaboom.js_ has the same syntax as _JS_ (Javascript). That's one reason why I believe _kaboom.js_ is so simple. This allows developers to easily change the starter code and create their own video games using the sandbox. The sandbox is where developers can try new things for their games. This is a very useful tool for _kaboom.js_ as a whole. That's what I believe overall. I also think that _kaboom.js_ can be used for collision handling. This is great for giving life throughout your game overall in this type of space.


### **_Challenges / Takeaways:_**

* One challenge I had was the fact that I didn't know anything about _kaboom.js_ when creating this entry.
	* _This was before I started researching things about kaboom.js._ 

* Another challenge I faced was that I didn't tinker enough while working on _kaboom.js_ entry.  
	* _*because I keep getting errors, crashes, and plain issues._ 

* One last challenge I had in my experience in this entry was that I didn't ask for help enough.
	* _Mostly because I didn't use slack or any other help._

* One takeaway I have was that I need to tinker more with my tool.
	* _Can because I can get a better knowing of what my tool can do._

* Another takeaway I got from this is that I need better time management skills in this space. 
	* _As in not doing everything last minute..._

* One last takeaway I have from this entry is that I would like to learn more from this point and knowing more about my tool will help me in the long run.





---




#### **__Links:__**


* [**_SEP11_ Notes**](https://docs.google.com/document/d/1Q65VlDlA7tV9fuXP8XF-dxXL7TYyunfjoUTJqYvnTyI/edit)

    * [JS-bin](https://jsbin.com/?js,output)

    * [Code Samples](https://docs.google.com/document/d/1-mfcBeP-TTHBOmmZzVRX3DUNUvo62zFFuJiPtEnw_aw/edit)

* [**The Full Students Repo**](https://github.com/hstatsep-students/)

    * [_SEP11_ Blog Page Overview](https://github.com/nayere6342/sep11-freedom-project/tree/main)

    * [_SEP11_ Entry Backup Doc](https://docs.google.com/document/d/1YnfwCAMGU6oB6WFvHh2m1oW8arNfXkU21ra7zhBruOA/edit?tab=t.0)




---


























[Next Entry](entry02.md)

[Back Home](../README.md)

