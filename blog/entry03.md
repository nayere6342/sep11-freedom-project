# **Entry Log 3:**
### **Nayer Ebraheim - 2/3/25**

---

## Intro:

For a bit of context. My third entry I have mostly learned the same sort of thing from the last entry. For my freedom project that I have started in _kaboom.js_ I have learned a ton of things such as how to create an object spawning loop. With this you can create a puzzle game or like an endless runner! But it can also lead to crashes through game object spawning. Now, on to the code presentation.   

## Code Presentation:

In this, it's mostly things from the last blog and this blog such as the spawning object loop script. By having this, it can make a simple game into a fun enjoyable game. Like I said before, I have learned a ton of things such as how to create an object spawning loop. With this you can create a puzzle game or like an endless runner! But it can also lead to crashes through game object spawning. By using _kaboom.js_ you can learn so much about how game development works in a small programming app. Now here is a quick preview of what was made before hand:     

```Js
const FLOOR_HEIGHT = 48
const JUMP_FORCE = 800
const SPEED = 480

// initialize context
kaboom()

setBackground(141, 183, 255)

// load assets
loadSprite("bean", "/sprites/bean.png")

scene("game", () => {

	// define gravity
	setGravity(2400)

	// add a game object to screen
	const player = add([
		// list of components
		sprite("bean"),
		pos(80, 40),
		area(),
		body(),
	])

	// floor
	add([
		rect(width(), FLOOR_HEIGHT),
		outline(4),
		pos(0, height()),
		anchor("botleft"),
		area(),
		body({ isStatic: true }),
		color(132, 101, 236),
	])

	function jump() {
		if (player.isGrounded()) {
			player.jump(JUMP_FORCE)
		}
	}

	// jump when user press space
	onKeyPress("space", jump)
	onClick(jump)

	function spawnTree() {

		// add tree obj
		add([
			rect(48, rand(32, 96)),
			area(),
			outline(4),
			pos(width(), height() - FLOOR_HEIGHT),
			anchor("botleft"),
			color(238, 143, 203),
			move(LEFT, SPEED),
			offscreen({ destroy: true }),
			"tree",
		])

		// wait a random amount of time to spawn next tree
		wait(rand(0.5, 1.5), spawnTree)

	}

	// start spawning trees
	spawnTree()

	// lose if player collides with any game obj with tag "tree"
	player.onCollide("tree", () => {
		// go to "lose" scene and pass the score
		go("lose", score)
		burp()
		addKaboom(player.pos)
	})

	// keep track of score
	let score = 0

	const scoreLabel = add([
		text(score),
		pos(24, 24),
	])

	// increment score every frame
	onUpdate(() => {
		score++
		scoreLabel.text = score
	})

})

scene("lose", (score) => {

	add([
		sprite("bean"),
		pos(width() / 2, height() / 2 - 64),
		scale(2),
		anchor("center"),
	])

	// display score
	add([
		text(score),
		pos(width() / 2, height() / 2 + 64),
		scale(2),
		anchor("center"),
	])

	// go back to game with space is pressed
	onKeyPress("space", () => go("game"))
	onClick(() => go("game"))

})

go("game")

```

First:

* The first part of the code is pretty simple. All it does is that it loads the game player into space.

* It also sets the gravity of the player as well as giving the player a jump function.

```Js
// load assets
loadSprite("bean", "/sprites/bean.png")

scene("game", () => {

	// define gravity
	setGravity(2400)

	// add a game object to screen
	const player = add([
		// list of components
		sprite("bean"),
		pos(80, 40),
		area(),
		body(),
	])

	// floor
	add([
		rect(width(), FLOOR_HEIGHT),
		outline(4),
		pos(0, height()),
		anchor("botleft"),
		area(),
		body({ isStatic: true }),
		color(132, 101, 236),
	])

	function jump() {
		if (player.isGrounded()) {
			player.jump(JUMP_FORCE)
		}
	}

	// jump when user press space
	onKeyPress("space", jump)
	onClick(jump)
```

Second:

* This part of the code just adds objects that the player has to jump over. 

* It also creates a scoring system for the player every time they cross a tree.

```Js
function spawnTree() {

		// add tree obj
		add([
			rect(48, rand(32, 96)),
			area(),
			outline(4),
			pos(width(), height() - FLOOR_HEIGHT),
			anchor("botleft"),
			color(238, 143, 203),
			move(LEFT, SPEED),
			offscreen({ destroy: true }),
			"tree",
		])

		// wait a random amount of time to spawn next tree
		wait(rand(0.5, 1.5), spawnTree)

	}

	// start spawning trees
	spawnTree()

	// lose if player collides with any game obj with tag "tree"
	player.onCollide("tree", () => {
		// go to "lose" scene and pass the score
		go("lose", score)
		burp()
		addKaboom(player.pos)
	})

	// keep track of score
	let score = 0

	const scoreLabel = add([
		text(score),
		pos(24, 24),
	])

	// increment score every frame
	onUpdate(() => {
		score++
		scoreLabel.text = score
	})

})
```

Third:

* Third of all is the loose screen. This clears all the objects on the screen.

* Then it loads the player character onto the screen as well as the player's final score.

```Js
scene("lose", (score) => {

	add([
		sprite("bean"),
		pos(width() / 2, height() / 2 - 64),
		scale(2),
		anchor("center"),
	])

	// display score
	add([
		text(score),
		pos(width() / 2, height() / 2 + 64),
		scale(2),
		anchor("center"),
	])

	// go back to game with space is pressed
	onKeyPress("space", () => go("game"))
	onClick(() => go("game"))

})

go("game")
```

## Design Process:

The design of this was that the collision objects were weird at first. Due to me, not know everything there is to know about this topic. Thus not getting a grip on what I was going to do in the beginning of this. Like how should the player's hitbox hit the object they should avoid in the game. I did have help from videos on youtube to help me get a grip on what I'm supposed to in this entry. That's what help me get to be a bit more knowing on this topic overall. That's my design process for this entry. It was a bit complex overall and thanks to these videos. I figured out how it all works. 

### **_Challenges / Takeaways:_**



* One big challenge I had in my experience in this entry was that I didn't ask for help enough.
  
	* _Mostly because I didn't use slack or any other help._

* One other challenge I had from this was the fact that my IDE keeped crashing throughout the whole tinker section. 

* Another challenge I faced was that I didn't tinker enough while working on _kaboom.js_ entry. Also, this could have been because of the fact that I keep getting errors, crashes, and planning issues.
   
* One small takeaway I had from this was that I need to tinker more with my tool.
  
	* _Can because I can get a better knowing of what my tool can do._

* Another takeaway I got from this is that I need better time management skills in this space.

	* As in not doing everything last minute...





---


#### **__Links:__**


* [**_SEP11_ Notes**](https://docs.google.com/document/d/1Q65VlDlA7tV9fuXP8XF-dxXL7TYyunfjoUTJqYvnTyI/edit)

    * [JS-bin](https://jsbin.com/?js,output)
    * [Code Samples](https://docs.google.com/document/d/1-mfcBeP-TTHBOmmZzVRX3DUNUvo62zFFuJiPtEnw_aw/edit)

* [**The Full Students Repo**](https://github.com/hstatsep-students/)

    * [_SEP11_ Entry Page Overview](https://github.com/nayere6342/sep11-freedom-project/tree/main)

    * [_SEP11_ Entry Backup Doc](https://docs.google.com/document/d/1YnfwCAMGU6oB6WFvHh2m1oW8arNfXkU21ra7zhBruOA/edit?tab=t.0)








---





[Previous](entry01.md) | [Next Entry](entry03.md)

[Back Home](../README.md)






