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

```JS
// Collision handling

// Start kaboom
kaboom({
	scale: 2,
})

// Load assets
loadSprite("bean", "/sprites/bean.png")
loadSprite("ghosty", "/sprites/ghosty.png")
loadSprite("grass", "/sprites/grass.png")
loadSprite("steel", "/sprites/steel.png")

// Define player movement speed
const SPEED = 320

// Add player game object
const player = add([
	sprite("bean"),
	pos(80, 40),
	color(),
	rotate(0),
	// area() component gives the object a collider, which enables collision checking
	area(),
	// area({ shape: new Polygon([vec2(0), vec2(100), vec2(-100, 100)]) }),
	// area({ shape: new Rect(vec2(0), 12, 120) }),
	// area({ scale: 0.5 }),
	// body() component makes an object respond to physics
	body(),
])

// Register input handlers & movement
onKeyDown("left", () => {
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

onKeyDown("q", () => {
	player.angle -= SPEED * dt()
})

onKeyDown("e", () => {
	player.angle += SPEED * dt()
})

// Add enemies
for (let i = 0; i < 3; i++) {

	const x = rand(0, width())
	const y = rand(0, height())

	add([
		sprite("ghosty"),
		pos(x, y),
		// Both objects must have area() component to enable collision detection between
		area(),
		"enemy",
	])

}

add([
	sprite("grass"),
	pos(center()),
	area(),
	// This game object also has isStatic, so our player won't be able to move past this
	body({ isStatic: true }),
	"grass",
])

add([
	sprite("steel"),
	pos(100, 200),
	area(),
	// This will not be static, but have a big mass that's hard to push over
	body({ mass: 10 }),
])

// .onCollide() is provided by area() component, it registers an event that runs when an objects collides with another object with certain tag
// In this case we destroy (remove from game) the enemy when player hits one
player.onCollide("enemy", (enemy) => {
	destroy(enemy)
})

// .onCollideUpdate() runs every frame when an object collides with another object
player.onCollideUpdate("enemy", () => {
})

// .onCollideEnd() runs once when an object stopped colliding with another object
player.onCollideEnd("grass", (a) => {
	debug.log("leave grass")
})

// .clicks() is provided by area() component, it registers an event that runs when the object is clicked
player.onClick(() => {
	debug.log("what up")
})

player.onUpdate(() => {
	// .isHovering() is provided by area() component, which returns a boolean of if the object is currently being hovered on
	if (player.isHovering()) {
		player.color = rgb(0, 0, 255)
	} else {
		player.color = rgb()
	}
})

// Enter inspect mode, which shows the collider outline of each object with area() component, handy for debugging
// Can also be toggled by pressing F1
debug.inspect = true

// Check out https://kaboomjs.com#AreaComp for everything area() provides
```

First part of the code fully loads all the assets. Then it gets the texture for those assets. For example `"/sprites/grass.png"` the code first gets into the folder called; `sprites`. After that, it gets the file called; `grass.png` Like I said before, I also think that _kaboom.js_ can be used for collision handling. This is great for giving life throughout your game overall in this type of space. As I just said, I believe collision handling can be great for giving life throughout your game as well as letting the player have more freedom in the game. For example the player has a fully customizable room where they can move everything by push. Or even a ball pit where all the balls in the pit are callable when the player moves around in the pit. Also, I believe that _kaboom.js_ can be good for handling basic _AI_. Such as this here:
   

```JS
// Use state() component to handle basic AI

// Start kaboom
kaboom()

// Load assets
loadSprite("bean", "/sprites/bean.png")
loadSprite("ghosty", "/sprites/ghosty.png")

const SPEED = 320
const ENEMY_SPEED = 16000
const BULLET_SPEED = 800

// Add player game object
const player = add([
	sprite("bean"),
	pos(80, 80),
	area(),
	anchor("center"),
])

const enemy = add([
	sprite("ghosty"),
	pos(width() - 80, height() - 80),
	anchor("center"),
	// This enemy cycle between 3 states, and start from "idle" state
	state("move", [ "idle", "attack", "move" ]),
])

// Run the callback once every time we enter an "idle" state.
// Here we stay "idle" for 0.5 second, then enter the "attack" state.
enemy.onStateEnter("idle", async () => {
	await wait(0.5)
	enemy.enterState("attack")
})

// When we enter "attack" state, we fire a bullet, and enter "move" state after 1 sec
enemy.onStateEnter("attack", async () => {

	// Don't do anything if player doesn't exist anymore
	if (player.exists()) {

		const dir = player.pos.sub(enemy.pos).unit()

		add([
			pos(enemy.pos),
			move(dir, BULLET_SPEED),
			rect(12, 12),
			area(),
			offscreen({ destroy: true }),
			anchor("center"),
			color(BLUE),
			"bullet",
		])

	}

	await wait(1)
	enemy.enterState("move")

})

enemy.onStateEnter("move", async () => {
	await wait(2)
	enemy.enterState("idle")
})

// Like .onUpdate() which runs every frame, but only runs when the current state is "move"
// Here we move towards the player every frame if the current state is "move"
enemy.onStateUpdate("move", () => {
	if (!player.exists()) return
	const dir = player.pos.sub(enemy.pos).unit()
	enemy.move(dir.scale(ENEMY_SPEED))
})

// Taking a bullet makes us disappear
player.onCollide("bullet", (bullet) => {
	destroy(bullet)
	destroy(player)
	addKaboom(bullet.pos)
})

// Register input handlers & movement
onKeyDown("left", () => {
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
```

This code is only for handling basic _AI_. I think _kaboom.js_  does basic _AI_ really well in this space by:

First: 
* Loading both the player character & enemy character.

* Then it sets the speed for the enemy character & player character as well as the enemy bullet

As shown in part of the _JS_ code;

```JS
// Load assets
loadSprite("bean", "/sprites/bean.png")
loadSprite("ghosty", "/sprites/ghosty.png")

const SPEED = 320
const ENEMY_SPEED = 16000
const BULLET_SPEED = 800

```

Second: 
* Setting a _”idle”_ & _”attack”_ state

* idle is the state the enemy is in if it doesn’t see the player

* attack is the state the enemy is in if it does see the player

**If the enemy is far away from the player it shoots at the player. If the enemy is close it hits the player. You can see this in this part of the code;

```JS
// Run the callback once every time we enter an "idle" state.
// Here we stay "idle" for 0.5 second, then enter the "attack" state.
enemy.onStateEnter("idle", async () => {
	await wait(0.5)
	enemy.enterState("attack")
})

// When we enter "attack" state, we fire a bullet, and enter "move" state after 1 sec
enemy.onStateEnter("attack", async () => {

	// Don't do anything if player doesn't exist anymore
	if (player.exists()) {

		const dir = player.pos.sub(enemy.pos).unit()

		add([
			pos(enemy.pos),
			move(dir, BULLET_SPEED),
			rect(12, 12),
			area(),
			offscreen({ destroy: true }),
			anchor("center"),
			color(BLUE),
			"bullet",
		])

	}

	await wait(1)
	enemy.enterState("move")

})

enemy.onStateEnter("move", async () => {
	await wait(2)
	enemy.enterState("idle")
})
```

Third:
* After all that, it calculates where in the grid the enemy should be relative to the player's position.

```JS
// Add player game object
const player = add([
	sprite("bean"),
	pos(80, 80),
	area(),
	anchor("center"),
])

const enemy = add([
	sprite("ghosty"),
	pos(width() - 80, height() - 80),
	anchor("center"),
	// This enemy cycle between 3 states, and start from "idle" state
	state("move", [ "idle", "attack", "move" ]),
])
```

That's what all the code does in this part of the game, As you can see this part of the code is very simple in most ways.



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

