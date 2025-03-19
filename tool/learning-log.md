# Tool Learning Log

## Tool: **Kaboom.js**

## Project: **Puzzle Platformer**

# **Section #1:**
### **Nayer Ebraheim - 10/28/24**

---

### Main Content

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



# **Section #2:**
### **Nayer Ebraheim - 11/11/24**

---

### Main Content

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

This mainline code is for the base for your game. This might be because of the fact that _kaboom.js_ has the same syntax as _JS_ (Javascript). That's one reason why I believe _kaboom.js_ is so simple. This allows developers to easily change the starter code and create their own video games using the sandbox. The sandbox is where developers can try new things for their games. This is a very useful tool for _kaboom.js_ as a whole. That's what I believe overall. I also think that _kaboom.js_ can be used for collision handling. This is great for giving life throughout your game overall in this type of space. As well as some challenges in this;


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


# **Section #3:**
### **Nayer Ebraheim - 11/3/24**

---

### Main Content

To start off my 3rd entry. Like I said before, I have been working on my freedom project _(FP)_. For quite a while. And I learned that _kaboom.js_ is more complex than I thought. So I started thinking with this tool. This was mostly due to the fact that I didn't really know a whole lot about my tool at the time. To show what I have been doing. Here is some code:

```JS
kaboom()

loadSprite("bean", "/sprites/bean.png")
loadSound("score", "/examples/sounds/score.mp3")
loadSound("wooosh", "/examples/sounds/wooosh.mp3")
loadSound("hit", "/examples/sounds/hit.mp3")

// define gravity
setGravity(3200)

scene("game", () => {

	const PIPE_OPEN = 240
	const PIPE_MIN = 60
	const JUMP_FORCE = 800
	const SPEED = 320
	const CEILING = -60

	// a game object consists of a list of components and tags
	const bean = add([
		// sprite() means it's drawn with a sprite of name "bean" (defined above in 'loadSprite')
		sprite("bean"),
		// give it a position
		pos(width() / 4, 0),
		// give it a collider
		area(),
		// body component enables it to fall and jump in a gravity world
		body(),
	])

	// check for fall death
	bean.onUpdate(() => {
		if (bean.pos.y >= height() || bean.pos.y <= CEILING) {
			// switch to "lose" scene
			go("lose", score)
		}
	})

	// jump
	onKeyPress("space", () => {
		bean.jump(JUMP_FORCE)
		play("wooosh")
	})

	onGamepadButtonPress("south", () => {
		bean.jump(JUMP_FORCE)
		play("wooosh")
	})

	// mobile
	onClick(() => {
		bean.jump(JUMP_FORCE)
		play("wooosh")
	})

	function spawnPipe() {

		// calculate pipe positions
		const h1 = rand(PIPE_MIN, height() - PIPE_MIN - PIPE_OPEN)
		const h2 = height() - h1 - PIPE_OPEN

		add([
			pos(width(), 0),
			rect(64, h1),
			color(0, 127, 255),
			outline(4),
			area(),
			move(LEFT, SPEED),
			offscreen({ destroy: true }),
			// give it tags to easier define behaviors see below
			"pipe",
		])

		add([
			pos(width(), h1 + PIPE_OPEN),
			rect(64, h2),
			color(0, 127, 255),
			outline(4),
			area(),
			move(LEFT, SPEED),
			offscreen({ destroy: true }),
			// give it tags to easier define behaviors see below
			"pipe",
			// raw obj just assigns every field to the game obj
			{ passed: false },
		])

	}

	// callback when bean onCollide with objects with tag "pipe"
	bean.onCollide("pipe", () => {
		go("lose", score)
		play("hit")
		addKaboom(bean.pos)
	})

	// per frame event for all objects with tag 'pipe'
	onUpdate("pipe", (p) => {
		// check if bean passed the pipe
		if (p.pos.x + p.width <= bean.pos.x && p.passed === false) {
			addScore()
			p.passed = true
		}
	})

	// spawn a pipe every 1 sec
	loop(1, () => {
		spawnPipe()
	})

	let score = 0

	// display score
	const scoreLabel = add([
		text(score),
		anchor("center"),
		pos(width() / 2, 80),
		fixed(),
		z(100),
	])

	function addScore() {
		score++
		scoreLabel.text = score
		play("score")
	}

})

scene("lose", (score) => {

	add([
		sprite("bean"),
		pos(width() / 2, height() / 2 - 108),
		scale(3),
		anchor("center"),
	])

	// display score
	add([
		text(score),
		pos(width() / 2, height() / 2 + 108),
		scale(3),
		anchor("center"),
	])

	// go back to game with space is pressed
	onKeyPress("space", () => go("game"))
	onClick(() => go("game"))

})

go("game")

```
This code is from _kaboom.js_ code snippets. All the code does is:

First

* creates a player sprite with gravity,
  
	* this play sprite also has a set speed,

shown here:

```JS
	const JUMP_FORCE = 800
	const SPEED = 320
	const CEILING = -60

	// a game object consists of a list of components and tags
	const bean = add([
		// sprite() means it's drawn with a sprite of name "bean" (defined above in 'loadSprite')
		sprite("bean"),
		// give it a position
		pos(width() / 4, 0),
		// give it a collider
		area(),
		// body component enables it to fall and jump in a gravity world
		body(),
	])

	// check for fall death
	bean.onUpdate(() => {
		if (bean.pos.y >= height() || bean.pos.y <= CEILING) {
			// switch to "lose" scene
			go("lose", score)
		}
	})

	// jump
	onKeyPress("space", () => {
		bean.jump(JUMP_FORCE)
		play("wooosh")
	})
```

Second: 

* It set up a death and final score screen,
  
	* The final score screen uses the amount of times the player jumps over the pipes,
   
 As shown here:

```JS
// callback when bean onCollide with objects with tag "pipe"
	bean.onCollide("pipe", () => {
		go("lose", score)
		play("hit")
		addKaboom(bean.pos)
	})
```

That's what mostly happened while I was tinkering with my tool. To recap in the last entry, I learn how sprites work in a game space shown here: But before we get into that, lets recap what over the past week in this learning log - section #3.

### Recap:

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

First part of the code fully loads all the assets. Then it gets the texture for those assets. For example `"/sprites/grass.png"` the code first gets into the folder called; `sprites`. After that, it gets the file called; `grass.png` Like I said before, I also think that _kaboom.js_ can be used for collision handling. This is great for giving life throughout your game overall in this type of space. As I just said, I believe collision handling can be great for giving life throughout your game as well as letting the player have more freedom in the game. For example the player has a fully customizable room where they can move everything by push. Or even a ball pit where all the balls in the pit are callable when the player moves around in the pit. Also, I believe that _kaboom.js_ can be good for handling basic _AI_.



### **_Challenges / Takeaways:_**

* One challenge I had was the fact that I didn't know anything about _kaboom.js_ when creating this entry.
	* _This was before I started researching things about kaboom.js._ 

* Another challenge I faced was that I didn't tinker enough while working on _kaboom.js_ entry.
  
	* _*because I keep getting errors, crashes, and plain issues._
   
	* _Mostly because I didn't use slack or any other help._

* One takeaway I have was that I need to tinker more with my tool.
	* _Can because I can get a better knowing of what my tool can do._

* Another takeaway I have from this entry is that I would like to learn more from this point and knowing more about my tool will help me in the long run.

* One last takeaway I got from this is that I need better time management skills in this space. 
	* _As in not doing everything last minute..._





# **Section #4:**
### **Nayer Ebraheim - 11/18/24**

---

To start off my entry, I have been working on my freedom project (FP). For a long time by this point. And, what I have learned is that _kaboom.js_ is much more simple than I thought. Last entry I stated that I thought it was complex overall. This was because I didn't really know how _kaboom.js_ work to begin with. Before I get into that, I will show you a quick recap!

### Recap:

This is a short recap to what I have learn in my last entry of this blog: _(entry 1)_

The sandbox is where developers can try new things for their games. This is a very useful tool for _kaboom.js_ as a whole. That's what I believe overall. I also think that _kaboom.js_ can be used for collision handling. This is great for giving life throughout your game overall in this type of space.

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

First part of the code fully loads all the assets. Then it gets the texture for those assets. For example `"/sprites/grass.png"` the code first gets into the folder called; `sprites`. After that, it gets the file called; `grass.png` Like I said before, I also think that _kaboom.js_ can be used for collision handling. This is great for giving life throughout your game overall in this type of space. As I just said, I believe collision handling can be great for giving life throughout your game as well as letting the player have more freedom in the game. For example the player has a fully customizable room where they can move everything by push. Or even a ball pit where all the balls in the pit are callable when the player moves around in the pit. Also, I believe that _kaboom.js_ can be good for handling basic _AI_.

As well as a recap to my most recent entry _(entry #3):_

To start off my 3rd entry. Like I said before, I have been working on my freedom project _(FP)_. For quite a while. And I learned that _kaboom.js_ is more complex than I thought. So I started thinking with this tool. This was mostly due to the fact that I didn't really know a whole lot about my tool at the time. To show what I have been doing. Here is some code:

```JS
kaboom()

loadSprite("bean", "/sprites/bean.png")
loadSound("score", "/examples/sounds/score.mp3")
loadSound("wooosh", "/examples/sounds/wooosh.mp3")
loadSound("hit", "/examples/sounds/hit.mp3")

// define gravity
setGravity(3200)

scene("game", () => {

	const PIPE_OPEN = 240
	const PIPE_MIN = 60
	const JUMP_FORCE = 800
	const SPEED = 320
	const CEILING = -60

	// a game object consists of a list of components and tags
	const bean = add([
		// sprite() means it's drawn with a sprite of name "bean" (defined above in 'loadSprite')
		sprite("bean"),
		// give it a position
		pos(width() / 4, 0),
		// give it a collider
		area(),
		// body component enables it to fall and jump in a gravity world
		body(),
	])

	// check for fall death
	bean.onUpdate(() => {
		if (bean.pos.y >= height() || bean.pos.y <= CEILING) {
			// switch to "lose" scene
			go("lose", score)
		}
	})

	// jump
	onKeyPress("space", () => {
		bean.jump(JUMP_FORCE)
		play("wooosh")
	})

	onGamepadButtonPress("south", () => {
		bean.jump(JUMP_FORCE)
		play("wooosh")
	})

	// mobile
	onClick(() => {
		bean.jump(JUMP_FORCE)
		play("wooosh")
	})

	function spawnPipe() {

		// calculate pipe positions
		const h1 = rand(PIPE_MIN, height() - PIPE_MIN - PIPE_OPEN)
		const h2 = height() - h1 - PIPE_OPEN

		add([
			pos(width(), 0),
			rect(64, h1),
			color(0, 127, 255),
			outline(4),
			area(),
			move(LEFT, SPEED),
			offscreen({ destroy: true }),
			// give it tags to easier define behaviors see below
			"pipe",
		])

		add([
			pos(width(), h1 + PIPE_OPEN),
			rect(64, h2),
			color(0, 127, 255),
			outline(4),
			area(),
			move(LEFT, SPEED),
			offscreen({ destroy: true }),
			// give it tags to easier define behaviors see below
			"pipe",
			// raw obj just assigns every field to the game obj
			{ passed: false },
		])

	}

	// callback when bean onCollide with objects with tag "pipe"
	bean.onCollide("pipe", () => {
		go("lose", score)
		play("hit")
		addKaboom(bean.pos)
	})

	// per frame event for all objects with tag 'pipe'
	onUpdate("pipe", (p) => {
		// check if bean passed the pipe
		if (p.pos.x + p.width <= bean.pos.x && p.passed === false) {
			addScore()
			p.passed = true
		}
	})

	// spawn a pipe every 1 sec
	loop(1, () => {
		spawnPipe()
	})

	let score = 0

	// display score
	const scoreLabel = add([
		text(score),
		anchor("center"),
		pos(width() / 2, 80),
		fixed(),
		z(100),
	])

	function addScore() {
		score++
		scoreLabel.text = score
		play("score")
	}

})

scene("lose", (score) => {

	add([
		sprite("bean"),
		pos(width() / 2, height() / 2 - 108),
		scale(3),
		anchor("center"),
	])

	// display score
	add([
		text(score),
		pos(width() / 2, height() / 2 + 108),
		scale(3),
		anchor("center"),
	])

	// go back to game with space is pressed
	onKeyPress("space", () => go("game"))
	onClick(() => go("game"))

})

go("game")

```
This code is from _kaboom.js_ code snippets. All the code does is:

First

* creates a player sprite with gravity,
  
	* this play sprite also has a set speed,

shown here:

```JS
	const JUMP_FORCE = 800
	const SPEED = 320
	const CEILING = -60

	// a game object consists of a list of components and tags
	const bean = add([
		// sprite() means it's drawn with a sprite of name "bean" (defined above in 'loadSprite')
		sprite("bean"),
		// give it a position
		pos(width() / 4, 0),
		// give it a collider
		area(),
		// body component enables it to fall and jump in a gravity world
		body(),
	])

	// check for fall death
	bean.onUpdate(() => {
		if (bean.pos.y >= height() || bean.pos.y <= CEILING) {
			// switch to "lose" scene
			go("lose", score)
		}
	})

	// jump
	onKeyPress("space", () => {
		bean.jump(JUMP_FORCE)
		play("wooosh")
	})
```

Moving on,

### Main Content

One thing I have learned throughout this entry process is that. One like I said before I have learned is that _kaboom.js_ is much more simple than I thought. Last entry I stated that I thought it was complex overall. This was because I didn't really know how _kaboom.js_ work to begin with. As well as the fact that it has gotten easier than ever for the whole tinkering process. This can be because of the simple fact that I have more knowledge on this subject of my tool _kaboom.js_ As shown here in this code snippet:

```JS
// simple rpg style walk and talk

kaboom({
	background: [74, 48, 82],
})

loadSprite("bag", "/sprites/bag.png")
loadSprite("ghosty", "/sprites/ghosty.png")
loadSprite("grass", "/sprites/grass.png")
loadSprite("steel", "/sprites/steel.png")
loadSprite("door", "/sprites/door.png")
loadSprite("key", "/sprites/key.png")
loadSprite("bean", "/sprites/bean.png")

scene("main", (levelIdx) => {

	const SPEED = 320

	// character dialog data
	const characters = {
		"a": {
			sprite: "bag",
			msg: "Hi Bean! You should get that key!",
		},
		"b": {
			sprite: "ghosty",
			msg: "Who are you? You can see me??",
		},
	}

	// level layouts
	const levels = [
		[
			"===|====",
			"=      =",
			"= $    =",
			"=    a =",
			"=      =",
			"=   @  =",
			"========",
		],
		[
			"--------",
			"-      -",
			"-   $  -",
			"|      -",
			"-    b -",
			"-  @   -",
			"--------",
		],
	]

	const level = addLevel(levels[levelIdx], {
		tileWidth: 64,
		tileHeight: 64,
		pos: vec2(64, 64),
		tiles: {
			"=": () => [
				sprite("grass"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
			],
			"-": () => [
				sprite("steel"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
			],
			"$": () => [
				sprite("key"),
				area(),
				anchor("center"),
				"key",
			],
			"@": () => [
				sprite("bean"),
				area(),
				body(),
				anchor("center"),
				"player",
			],
			"|": () => [
				sprite("door"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
				"door",
			],
		},
		// any() is a special function that gets called everytime there's a
		// symbol not defined above and is supposed to return what that symbol
		// means
		wildcardTile(ch) {
			const char = characters[ch]
			if (char) {
				return [
					sprite(char.sprite),
					area(),
					body({ isStatic: true }),
					anchor("center"),
					"character",
					{ msg: char.msg },
				]
			}
		},
	})

	// get the player game obj by tag
	const player = level.get("player")[0]

	function addDialog() {
		const h = 160
		const pad = 16
		const bg = add([
			pos(0, height() - h),
			rect(width(), h),
			color(0, 0, 0),
			z(100),
		])
		const txt = add([
			text("", {
				width: width(),
			}),
			pos(0 + pad, height() - h + pad),
			z(100),
		])
		bg.hidden = true
		txt.hidden = true
		return {
			say(t) {
				txt.text = t
				bg.hidden = false
				txt.hidden = false
			},
			dismiss() {
				if (!this.active()) {
					return
				}
				txt.text = ""
				bg.hidden = true
				txt.hidden = true
			},
			active() {
				return !bg.hidden
			},
			destroy() {
				bg.destroy()
				txt.destroy()
			},
		}
	}

	let hasKey = false
	const dialog = addDialog()

	player.onCollide("key", (key) => {
		destroy(key)
		hasKey = true
	})

	player.onCollide("door", () => {
		if (hasKey) {
			if (levelIdx + 1 < levels.length) {
				go("main", levelIdx + 1)
			} else {
				go("win")
			}
		} else {
			dialog.say("you got no key!")
		}
	})

	// talk on touch
	player.onCollide("character", (ch) => {
		dialog.say(ch.msg)
	})

	const dirs = {
		"left": LEFT,
		"right": RIGHT,
		"up": UP,
		"down": DOWN,
	}

	for (const dir in dirs) {
		onKeyPress(dir, () => {
			dialog.dismiss()
		})
		onKeyDown(dir, () => {
			player.move(dirs[dir].scale(SPEED))
		})
	}

})

scene("win", () => {
	add([
		text("You Win!"),
		pos(width() / 2, height() / 2),
		anchor("center"),
	])
})

go("main", 0)

```

In this code snippet it is supposed to present a RPG in _kaboom.js_. To summarize what's going on in this code snippet:

First:

* It loads the level room as well the player sprite and the NPC sprite,

* As well as setting the player's default speed,

```JS
kaboom({
	background: [74, 48, 82],
})

loadSprite("bag", "/sprites/bag.png")
loadSprite("ghosty", "/sprites/ghosty.png")
loadSprite("grass", "/sprites/grass.png")
loadSprite("steel", "/sprites/steel.png")
loadSprite("door", "/sprites/door.png")
loadSprite("key", "/sprites/key.png")
loadSprite("bean", "/sprites/bean.png")

scene("main", (levelIdx) => {

	const SPEED = 320

	// character dialog data
	const characters = {
		"a": {
			sprite: "bag",
			msg: "Hi Bean! You should get that key!",
		},
		"b": {
			sprite: "ghosty",
			msg: "Who are you? You can see me??",
		},
	}

	// level layouts
	const levels = [
		[
			"===|====",
			"=      =",
			"= $    =",
			"=    a =",
			"=      =",
			"=   @  =",
			"========",
		],
		[
			"--------",
			"-      -",
			"-   $  -",
			"|      -",
			"-    b -",
			"-  @   -",
			"--------",
		],
	]

	const level = addLevel(levels[levelIdx], {
		tileWidth: 64,
		tileHeight: 64,
		pos: vec2(64, 64),
		tiles: {
			"=": () => [
				sprite("grass"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
			],
			"-": () => [
				sprite("steel"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
			],
			"$": () => [
				sprite("key"),
				area(),
				anchor("center"),
				"key",
			],
			"@": () => [
				sprite("bean"),
				area(),
				body(),
				anchor("center"),
				"player",
			],
			"|": () => [
				sprite("door"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
				"door",
			],
		},
```

Second:

* After that, the game loads all the game objects in the space,

* Then it get the height, position and width of the level layout,

```JS
// get the player game obj by tag
	const player = level.get("player")[0]

	function addDialog() {
		const h = 160
		const pad = 16
		const bg = add([
			pos(0, height() - h),
			rect(width(), h),
			color(0, 0, 0),
			z(100),
		])
		const txt = add([
			text("", {
				width: width(),
			}),
			pos(0 + pad, height() - h + pad),
			z(100),
		])
		bg.hidden = true
		txt.hidden = true
		return {
			say(t) {
				txt.text = t
				bg.hidden = false
				txt.hidden = false
			},
			dismiss() {
				if (!this.active()) {
					return
				}
				txt.text = ""
				bg.hidden = true
				txt.hidden = true
			},
			active() {
				return !bg.hidden
			},
			destroy() {
				bg.destroy()
				txt.destroy()
			},
		}
	}

	let hasKey = false
	const dialog = addDialog()

	player.onCollide("key", (key) => {
		destroy(key)
		hasKey = true
	})

	player.onCollide("door", () => {
		if (hasKey) {
			if (levelIdx + 1 < levels.length) {
				go("main", levelIdx + 1)
			} else {
				go("win")
			}
		} else {
			dialog.say("you got no key!")
		}
	})

	// talk on touch
	player.onCollide("character", (ch) => {
		dialog.say(ch.msg)
	})

	const dirs = {
		"left": LEFT,
		"right": RIGHT,
		"up": UP,
		"down": DOWN,
	}
```

Third:

* Lastly, this part of the code snippet just get the player game object by tag,

* As well as giving the main NPC dialog with the `addDialog` function.

```JS
		// any() is a special function that gets called everytime there's a
		// symbol not defined above and is supposed to return what that symbol
		// means
		wildcardTile(ch) {
			const char = characters[ch]
			if (char) {
				return [
					sprite(char.sprite),
					area(),
					body({ isStatic: true }),
					anchor("center"),
					"character",
					{ msg: char.msg },
				]
			}
		},
	})

	// get the player game obj by tag
	const player = level.get("player")[0]

	function addDialog() {
		const h = 160
		const pad = 16
		const bg = add([
			pos(0, height() - h),
			rect(width(), h),
			color(0, 0, 0),
			z(100),
		])
		const txt = add([
			text("", {
				width: width(),
			}),
			pos(0 + pad, height() - h + pad),
			z(100),
		])
		bg.hidden = true
		txt.hidden = true
		return {
			say(t) {
				txt.text = t
				bg.hidden = false
				txt.hidden = false
			},
			dismiss() {
				if (!this.active()) {
					return
				}
				txt.text = ""
				bg.hidden = true
				txt.hidden = true
			},
			active() {
				return !bg.hidden
			},
			destroy() {
				bg.destroy()
				txt.destroy()
			},
		}
	}

	let hasKey = false
	const dialog = addDialog()

	player.onCollide("key", (key) => {
		destroy(key)
		hasKey = true
	})

	player.onCollide("door", () => {
		if (hasKey) {
			if (levelIdx + 1 < levels.length) {
				go("main", levelIdx + 1)
			} else {
				go("win")
			}
		} else {
			dialog.say("you got no key!")
		}
	})

	// talk on touch
	player.onCollide("character", (ch) => {
		dialog.say(ch.msg)
	})

	const dirs = {
		"left": LEFT,
		"right": RIGHT,
		"up": UP,
		"down": DOWN,
	}

	for (const dir in dirs) {
		onKeyPress(dir, () => {
			dialog.dismiss()
		})
		onKeyDown(dir, () => {
			player.move(dirs[dir].scale(SPEED))
		})
	}

})

scene("win", () => {
	add([
		text("You Win!"),
		pos(width() / 2, height() / 2),
		anchor("center"),
	])
})

go("main", 0)

```

That's all with this entry!



### **_Challenges / Takeaways:_**

* One challenge I had was that it felt like a whole lot of time to really tinker with this entry.

* One challenge I had was the fact that I didn't know anything about _kaboom.js_ when creating this entry.
  
	* _This was before I started researching things about kaboom.js._ 

* Another challenge I faced was that I didn't tinker enough while working on _kaboom.js_ entry.
  
	* _*because I keep getting errors, crashes, and plain issues._
   
	* _As well as mostly because of the fact that I didn't use slack or any other help._

* One takeaway I have was that I need to tinker more with my tool.
  
	* _Can because I can get a better knowing of what my tool can do._

* Another takeaway I have from this entry is that I would like to learn more from this point and knowing more about my tool will help me in the long run.

* One last takeaway I got from this is that I need better time management skills in this space.
  
	* _As in not doing everything last minute..._



# **Section #5:**
### **Nayer Ebraheim - 12/2/24**

---

For starters, this log was fun, I have learned a whole lot about _kaboom.js_. Such as how to create a player event call. This allows parts of the game to react to the environment. That one task in the game can make the whole game feel more alive in this context. But, before I get into that, a recap is need from the last learning log,

### Recap:

**This is the recap for _(entry #4)_**

One thing I have learned throughout this entry process is that. Like I said before, I have learned that kaboom.js is much more simple than I thought. Last entry I stated that I thought it was complex overall. This was because I didn't really know how kaboom.js work to begin with. As well as the fact that it has gotten easier than ever for the whole tinkering process. This can be because of the simple fact that I have more knowledge on this subject of my tool kaboom.js As shown here in this code snippet:

```JS
// simple rpg style walk and talk

kaboom({
	background: [74, 48, 82],
})

loadSprite("bag", "/sprites/bag.png")
loadSprite("ghosty", "/sprites/ghosty.png")
loadSprite("grass", "/sprites/grass.png")
loadSprite("steel", "/sprites/steel.png")
loadSprite("door", "/sprites/door.png")
loadSprite("key", "/sprites/key.png")
loadSprite("bean", "/sprites/bean.png")

scene("main", (levelIdx) => {

	const SPEED = 320

	// character dialog data
	const characters = {
		"a": {
			sprite: "bag",
			msg: "Hi Bean! You should get that key!",
		},
		"b": {
			sprite: "ghosty",
			msg: "Who are you? You can see me??",
		},
	}

	// level layouts
	const levels = [
		[
			"===|====",
			"=      =",
			"= $    =",
			"=    a =",
			"=      =",
			"=   @  =",
			"========",
		],
		[
			"--------",
			"-      -",
			"-   $  -",
			"|      -",
			"-    b -",
			"-  @   -",
			"--------",
		],
	]

	const level = addLevel(levels[levelIdx], {
		tileWidth: 64,
		tileHeight: 64,
		pos: vec2(64, 64),
		tiles: {
			"=": () => [
				sprite("grass"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
			],
			"-": () => [
				sprite("steel"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
			],
			"$": () => [
				sprite("key"),
				area(),
				anchor("center"),
				"key",
			],
			"@": () => [
				sprite("bean"),
				area(),
				body(),
				anchor("center"),
				"player",
			],
			"|": () => [
				sprite("door"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
				"door",
			],
		},
		// any() is a special function that gets called everytime there's a
		// symbol not defined above and is supposed to return what that symbol
		// means
		wildcardTile(ch) {
			const char = characters[ch]
			if (char) {
				return [
					sprite(char.sprite),
					area(),
					body({ isStatic: true }),
					anchor("center"),
					"character",
					{ msg: char.msg },
				]
			}
		},
	})

	// get the player game obj by tag
	const player = level.get("player")[0]

	function addDialog() {
		const h = 160
		const pad = 16
		const bg = add([
			pos(0, height() - h),
			rect(width(), h),
			color(0, 0, 0),
			z(100),
		])
		const txt = add([
			text("", {
				width: width(),
			}),
			pos(0 + pad, height() - h + pad),
			z(100),
		])
		bg.hidden = true
		txt.hidden = true
		return {
			say(t) {
				txt.text = t
				bg.hidden = false
				txt.hidden = false
			},
			dismiss() {
				if (!this.active()) {
					return
				}
				txt.text = ""
				bg.hidden = true
				txt.hidden = true
			},
			active() {
				return !bg.hidden
			},
			destroy() {
				bg.destroy()
				txt.destroy()
			},
		}
	}

	let hasKey = false
	const dialog = addDialog()

	player.onCollide("key", (key) => {
		destroy(key)
		hasKey = true
	})

	player.onCollide("door", () => {
		if (hasKey) {
			if (levelIdx + 1 < levels.length) {
				go("main", levelIdx + 1)
			} else {
				go("win")
			}
		} else {
			dialog.say("you got no key!")
		}
	})

	// talk on touch
	player.onCollide("character", (ch) => {
		dialog.say(ch.msg)
	})

	const dirs = {
		"left": LEFT,
		"right": RIGHT,
		"up": UP,
		"down": DOWN,
	}

	for (const dir in dirs) {
		onKeyPress(dir, () => {
			dialog.dismiss()
		})
		onKeyDown(dir, () => {
			player.move(dirs[dir].scale(SPEED))
		})
	}

})

scene("win", () => {
	add([
		text("You Win!"),
		pos(width() / 2, height() / 2),
		anchor("center"),
	])
})

go("main", 0)
```

In this code snippet it is supposed to present a RPG in kaboom.js. To summarize what's going on in this code snippet:

First:

It loads the level room as well the player sprite and the NPC sprite,

As well as setting the player's default speed,

```JS
kaboom({
	background: [74, 48, 82],
})

loadSprite("bag", "/sprites/bag.png")
loadSprite("ghosty", "/sprites/ghosty.png")
loadSprite("grass", "/sprites/grass.png")
loadSprite("steel", "/sprites/steel.png")
loadSprite("door", "/sprites/door.png")
loadSprite("key", "/sprites/key.png")
loadSprite("bean", "/sprites/bean.png")

scene("main", (levelIdx) => {

	const SPEED = 320

	// character dialog data
	const characters = {
		"a": {
			sprite: "bag",
			msg: "Hi Bean! You should get that key!",
		},
		"b": {
			sprite: "ghosty",
			msg: "Who are you? You can see me??",
		},
	}

	// level layouts
	const levels = [
		[
			"===|====",
			"=      =",
			"= $    =",
			"=    a =",
			"=      =",
			"=   @  =",
			"========",
		],
		[
			"--------",
			"-      -",
			"-   $  -",
			"|      -",
			"-    b -",
			"-  @   -",
			"--------",
		],
	]

	const level = addLevel(levels[levelIdx], {
		tileWidth: 64,
		tileHeight: 64,
		pos: vec2(64, 64),
		tiles: {
			"=": () => [
				sprite("grass"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
			],
			"-": () => [
				sprite("steel"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
			],
			"$": () => [
				sprite("key"),
				area(),
				anchor("center"),
				"key",
			],
			"@": () => [
				sprite("bean"),
				area(),
				body(),
				anchor("center"),
				"player",
			],
			"|": () => [
				sprite("door"),
				area(),
				body({ isStatic: true }),
				anchor("center"),
				"door",
			],
		},
```

Second:

After that, the game loads all the game objects in the space,

Then it get the height, position and width of the level layout,

```JS
// get the player game obj by tag
	const player = level.get("player")[0]

	function addDialog() {
		const h = 160
		const pad = 16
		const bg = add([
			pos(0, height() - h),
			rect(width(), h),
			color(0, 0, 0),
			z(100),
		])
		const txt = add([
			text("", {
				width: width(),
			}),
			pos(0 + pad, height() - h + pad),
			z(100),
		])
		bg.hidden = true
		txt.hidden = true
		return {
			say(t) {
				txt.text = t
				bg.hidden = false
				txt.hidden = false
			},
			dismiss() {
				if (!this.active()) {
					return
				}
				txt.text = ""
				bg.hidden = true
				txt.hidden = true
			},
			active() {
				return !bg.hidden
			},
			destroy() {
				bg.destroy()
				txt.destroy()
			},
		}
	}

	let hasKey = false
	const dialog = addDialog()

	player.onCollide("key", (key) => {
		destroy(key)
		hasKey = true
	})

	player.onCollide("door", () => {
		if (hasKey) {
			if (levelIdx + 1 < levels.length) {
				go("main", levelIdx + 1)
			} else {
				go("win")
			}
		} else {
			dialog.say("you got no key!")
		}
	})

	// talk on touch
	player.onCollide("character", (ch) => {
		dialog.say(ch.msg)
	})

	const dirs = {
		"left": LEFT,
		"right": RIGHT,
		"up": UP,
		"down": DOWN,
	}
```
Third:

Lastly, this part of the code snippet just get the player game object by tag,

As well as giving the main NPC dialog with the addDialog function.

```JS
		// any() is a special function that gets called everytime there's a
		// symbol not defined above and is supposed to return what that symbol
		// means
		wildcardTile(ch) {
			const char = characters[ch]
			if (char) {
				return [
					sprite(char.sprite),
					area(),
					body({ isStatic: true }),
					anchor("center"),
					"character",
					{ msg: char.msg },
				]
			}
		},
	})

	// get the player game obj by tag
	const player = level.get("player")[0]

	function addDialog() {
		const h = 160
		const pad = 16
		const bg = add([
			pos(0, height() - h),
			rect(width(), h),
			color(0, 0, 0),
			z(100),
		])
		const txt = add([
			text("", {
				width: width(),
			}),
			pos(0 + pad, height() - h + pad),
			z(100),
		])
		bg.hidden = true
		txt.hidden = true
		return {
			say(t) {
				txt.text = t
				bg.hidden = false
				txt.hidden = false
			},
			dismiss() {
				if (!this.active()) {
					return
				}
				txt.text = ""
				bg.hidden = true
				txt.hidden = true
			},
			active() {
				return !bg.hidden
			},
			destroy() {
				bg.destroy()
				txt.destroy()
			},
		}
	}

	let hasKey = false
	const dialog = addDialog()

	player.onCollide("key", (key) => {
		destroy(key)
		hasKey = true
	})

	player.onCollide("door", () => {
		if (hasKey) {
			if (levelIdx + 1 < levels.length) {
				go("main", levelIdx + 1)
			} else {
				go("win")
			}
		} else {
			dialog.say("you got no key!")
		}
	})

	// talk on touch
	player.onCollide("character", (ch) => {
		dialog.say(ch.msg)
	})

	const dirs = {
		"left": LEFT,
		"right": RIGHT,
		"up": UP,
		"down": DOWN,
	}

	for (const dir in dirs) {
		onKeyPress(dir, () => {
			dialog.dismiss()
		})
		onKeyDown(dir, () => {
			player.move(dirs[dir].scale(SPEED))
		})
	}

})

scene("win", () => {
	add([
		text("You Win!"),
		pos(width() / 2, height() / 2),
		anchor("center"),
	])
})

go("main", 0)
```

Moving on from this recap,

### Main Content

These are all the things that I have learned with this entry. In _entry #4_ I have learned that like I said before. One thing I have learned throughout this entry process is that. I have learned that _kaboom.js_ is much more simple than I thought. Last entry I stated that I thought it was complex overall. This was because I didn't really know how _kaboom.js_ work to begin with. As well as the fact that it has gotten easier than ever for the whole tinkering process. As well as the fact that in this code snippet it shows that what I was just talking about how I have learned is that _kaboom.js_ is much more simple than I thought. All shown here;

```JS
// Kaboom as pure rendering lib (no component / game obj etc.)

kaboom()
loadSprite("bean", "/sprites/bean.png")

loadShader("spiral", null, `
uniform float u_time;
uniform vec2 u_mpos;
vec4 frag(vec2 pos, vec2 uv, vec4 color, sampler2D tex) {
	vec2 pp = uv - u_mpos;
	float angle = atan(pp.y, pp.x);
	float dis = length(pp);
	float c = sin(dis * 48.0 + u_time * 8.0 + angle);
	return vec4(c, c, c, 1);
}
`)

const t = (n = 1) => time() * n
const w = (a, b, n) => wave(a, b, t(n))
const px = 160
const py = 160
const doodles = []
const trail = []

const outline = {
	width: 4,
	color: rgb(0, 0, 0),
}

function drawStuff() {

	const mx = (width() - px * 2) / 2
	const my = (height() - py * 2) / 1
	const p = (x, y) => vec2(x, y).scale(mx, my).add(px, py)

	drawSprite({
		sprite: "bean",
		pos: p(0, 0),
		angle: t(40),
		anchor: "center",
		scale: w(1, 1.5, 4),
		color: rgb(w(128, 255, 4), w(128, 255, 8), 255),
	})

	drawRect({
		pos: p(1, 0),
		width: w(60, 120, 4),
		height: w(100, 140, 8),
		anchor: "center",
		radius: w(0, 32, 4),
		angle: t(80),
		color: rgb(w(128, 255, 4), 255, w(128, 255, 8)),
		outline,
	})

	drawEllipse({
		pos: p(2, 0),
		radiusX: w(40, 70, 2),
		radiusY: w(40, 70, 4),
		start: 0,
		end: w(180, 360, 1),
		color: rgb(255, w(128, 255, 8), w(128, 255, 4)),
		// gradient: [ Color.RED, Color.BLUE ],
		outline,
	})

	drawPolygon({
		pos: p(0, 1),
		pts: [
			vec2(w(-10, 10, 2), -80),
			vec2(80, w(-10, 10, 4)),
			vec2(w(30, 50, 4), 80),
			vec2(-30, w(50, 70, 2)),
			vec2(w(-50, -70, 4), 0),
		],
		colors: [
			rgb(w(128, 255, 8), 255, w(128, 255, 4)),
			rgb(255, w(128, 255, 8), w(128, 255, 4)),
			rgb(w(128, 255, 8), w(128, 255, 4), 255),
			rgb(255, 128, w(128, 255, 4)),
			rgb(w(128, 255, 8), w(128, 255, 4), 128),
		],
		outline,
	})

	drawText({
		text: "yo",
		pos: p(1, 1),
		anchor: "center",
		size: w(80, 120, 2),
		color: rgb(w(128, 255, 4), w(128, 255, 8), w(128, 255, 2)),
	})

	drawLines({
		...outline,
		pts: trail,
	})

	doodles.forEach((pts) => {
		drawLines({
			...outline,
			pts: pts,
		})
	})

}

// onDraw() is similar to onUpdate(), it runs every frame, but after all update events.
// All drawXXX() functions need to be called every frame if you want them to persist
onDraw(() => {

	const maskFunc = Math.floor(time()) % 2 === 0 ? drawSubtracted : drawMasked

	if (isKeyDown("space")) {
		maskFunc(() => {
			drawUVQuad({
				width: width(),
				height: height(),
				shader: "spiral",
				uniform: {
					"u_time": time(),
					"u_mpos": mousePos().scale(1 / width(), 1 / height()),
				},
			})
		}, drawStuff)
	} else {
		drawStuff()
	}

})

// It's a common practice to put all input handling and state updates before rendering.
onUpdate(() => {

	trail.push(mousePos())

	if (trail.length > 16) {
		trail.shift()
	}

	if (isMousePressed()) {
		doodles.push([])
	}

	if (isMouseDown() && isMouseMoved()) {
		doodles[doodles.length - 1].push(mousePos())
	}

})

```

As you can see here, It shows the groundwork for a simple rendering system in _kaboom.js_. Using the user's mouse. This is the full explanation for this code snippet;


First:

 * The script first tries to find the user's mouse in the window space.
   
 * then, script loads the following sprite for the mouse.

	* This just simply adds a shader to the mouse without using any inputs

```JS
loadShader("spiral", null, `
uniform float u_time;
uniform vec2 u_mpos;
vec4 frag(vec2 pos, vec2 uv, vec4 color, sampler2D tex) {
	vec2 pp = uv - u_mpos;
	float angle = atan(pp.y, pp.x);
	float dis = length(pp);
	float c = sin(dis * 48.0 + u_time * 8.0 + angle);
	return vec4(c, c, c, 1);
}
`)
```

Second:

 * After the script finds the user's mouse, it draws a trail that follows the user's mouse.

 * Once it's done with that, it waits until the user's clicks on something.

 * It takes that position, and draws an outline around that part of the screen where the user's clicks.

```JS
const t = (n = 1) => time() * n
const w = (a, b, n) => wave(a, b, t(n))
const px = 160
const py = 160
const doodles = []
const trail = []

const outline = {
	width: 4,
	color: rgb(0, 0, 0),
}

``` 

Third:

 * At the very end of the script, the scene is drawn into the window.

 * **This is only for the art style of the window**
   
 	* _This is not that important overall_
    
  * Also this might be because of the fact that it's pretty common to put all input handling and state updates before rendering begins.
  	*  
 

```JS
function drawStuff() {

	const mx = (width() - px * 2) / 2
	const my = (height() - py * 2) / 1
	const p = (x, y) => vec2(x, y).scale(mx, my).add(px, py)

	drawSprite({
		sprite: "bean",
		pos: p(0, 0),
		angle: t(40),
		anchor: "center",
		scale: w(1, 1.5, 4),
		color: rgb(w(128, 255, 4), w(128, 255, 8), 255),
	})

	drawRect({
		pos: p(1, 0),
		width: w(60, 120, 4),
		height: w(100, 140, 8),
		anchor: "center",
		radius: w(0, 32, 4),
		angle: t(80),
		color: rgb(w(128, 255, 4), 255, w(128, 255, 8)),
		outline,
	})

	drawEllipse({
		pos: p(2, 0),
		radiusX: w(40, 70, 2),
		radiusY: w(40, 70, 4),
		start: 0,
		end: w(180, 360, 1),
		color: rgb(255, w(128, 255, 8), w(128, 255, 4)),
		// gradient: [ Color.RED, Color.BLUE ],
		outline,
	})

	drawPolygon({
		pos: p(0, 1),
		pts: [
			vec2(w(-10, 10, 2), -80),
			vec2(80, w(-10, 10, 4)),
			vec2(w(30, 50, 4), 80),
			vec2(-30, w(50, 70, 2)),
			vec2(w(-50, -70, 4), 0),
		],
		colors: [
			rgb(w(128, 255, 8), 255, w(128, 255, 4)),
			rgb(255, w(128, 255, 8), w(128, 255, 4)),
			rgb(w(128, 255, 8), w(128, 255, 4), 255),
			rgb(255, 128, w(128, 255, 4)),
			rgb(w(128, 255, 8), w(128, 255, 4), 128),
		],
		outline,
	})

	drawText({
		text: "yo",
		pos: p(1, 1),
		anchor: "center",
		size: w(80, 120, 2),
		color: rgb(w(128, 255, 4), w(128, 255, 8), w(128, 255, 2)),
	})

	drawLines({
		...outline,
		pts: trail,
	})

	doodles.forEach((pts) => {
		drawLines({
			...outline,
			pts: pts,
		})
	})

}

// onDraw() is similar to onUpdate(), it runs every frame, but after all update events.
// All drawXXX() functions need to be called every frame if you want them to persist
onDraw(() => {

	const maskFunc = Math.floor(time()) % 2 === 0 ? drawSubtracted : drawMasked

	if (isKeyDown("space")) {
		maskFunc(() => {
			drawUVQuad({
				width: width(),
				height: height(),
				shader: "spiral",
				uniform: {
					"u_time": time(),
					"u_mpos": mousePos().scale(1 / width(), 1 / height()),
				},
			})
		}, drawStuff)
	} else {
		drawStuff()
	}

})

// It's a common practice to put all input handling and state updates before rendering.
onUpdate(() => {

	trail.push(mousePos())

	if (trail.length > 16) {
		trail.shift()
	}

	if (isMousePressed()) {
		doodles.push([])
	}

	if (isMouseDown() && isMouseMoved()) {
		doodles[doodles.length - 1].push(mousePos())
	}

})

```

### **_Challenges / Takeaways:_**

* One challenge I had was that it felt like a whole lot of time to really tinker with this entry.

* One challenge I had was the fact that I didn't know anything about _kaboom.js_ when creating this entry.
  
	* _This was before I started researching things about kaboom.js._ 

* Another challenge I faced was that I didn't tinker enough while working on _kaboom.js_ entry.
  
	* _*because I keep getting errors, crashes, and plain issues._
   

* One takeaway I have was that I need to tinker more with my tool.
  
	* _Can because I can get a better knowing of what my tool can do._

* One other takeaway I had from this learning log was that I need better time management skills in this space.
  
	* _As in not doing everything last minute..._

# **Section #6:**
### **Nayer Ebraheim - 12/17/24**

---

### Recap

These are all the things that I have learned with this entry. In _entry #4_ I have learned that like I said before. One thing I have learned throughout this entry process is that. I have learned that _kaboom.js_ is much more simple than I thought. Last entry I stated that I thought it was complex overall. This was because I didn't really know how _kaboom.js_ work to begin with. As well as the fact that it has gotten easier than ever for the whole tinkering process. As well as the fact that in this code snippet it shows that what I was just talking about how I have learned is that _kaboom.js_ is much more simple than I thought. All shown here;

```JS
// Kaboom as pure rendering lib (no component / game obj etc.)

kaboom()
loadSprite("bean", "/sprites/bean.png")

loadShader("spiral", null, `
uniform float u_time;
uniform vec2 u_mpos;
vec4 frag(vec2 pos, vec2 uv, vec4 color, sampler2D tex) {
	vec2 pp = uv - u_mpos;
	float angle = atan(pp.y, pp.x);
	float dis = length(pp);
	float c = sin(dis * 48.0 + u_time * 8.0 + angle);
	return vec4(c, c, c, 1);
}
`)

const t = (n = 1) => time() * n
const w = (a, b, n) => wave(a, b, t(n))
const px = 160
const py = 160
const doodles = []
const trail = []

const outline = {
	width: 4,
	color: rgb(0, 0, 0),
}

function drawStuff() {

	const mx = (width() - px * 2) / 2
	const my = (height() - py * 2) / 1
	const p = (x, y) => vec2(x, y).scale(mx, my).add(px, py)

	drawSprite({
		sprite: "bean",
		pos: p(0, 0),
		angle: t(40),
		anchor: "center",
		scale: w(1, 1.5, 4),
		color: rgb(w(128, 255, 4), w(128, 255, 8), 255),
	})

	drawRect({
		pos: p(1, 0),
		width: w(60, 120, 4),
		height: w(100, 140, 8),
		anchor: "center",
		radius: w(0, 32, 4),
		angle: t(80),
		color: rgb(w(128, 255, 4), 255, w(128, 255, 8)),
		outline,
	})

	drawEllipse({
		pos: p(2, 0),
		radiusX: w(40, 70, 2),
		radiusY: w(40, 70, 4),
		start: 0,
		end: w(180, 360, 1),
		color: rgb(255, w(128, 255, 8), w(128, 255, 4)),
		// gradient: [ Color.RED, Color.BLUE ],
		outline,
	})

	drawPolygon({
		pos: p(0, 1),
		pts: [
			vec2(w(-10, 10, 2), -80),
			vec2(80, w(-10, 10, 4)),
			vec2(w(30, 50, 4), 80),
			vec2(-30, w(50, 70, 2)),
			vec2(w(-50, -70, 4), 0),
		],
		colors: [
			rgb(w(128, 255, 8), 255, w(128, 255, 4)),
			rgb(255, w(128, 255, 8), w(128, 255, 4)),
			rgb(w(128, 255, 8), w(128, 255, 4), 255),
			rgb(255, 128, w(128, 255, 4)),
			rgb(w(128, 255, 8), w(128, 255, 4), 128),
		],
		outline,
	})

	drawText({
		text: "yo",
		pos: p(1, 1),
		anchor: "center",
		size: w(80, 120, 2),
		color: rgb(w(128, 255, 4), w(128, 255, 8), w(128, 255, 2)),
	})

	drawLines({
		...outline,
		pts: trail,
	})

	doodles.forEach((pts) => {
		drawLines({
			...outline,
			pts: pts,
		})
	})

}

// onDraw() is similar to onUpdate(), it runs every frame, but after all update events.
// All drawXXX() functions need to be called every frame if you want them to persist
onDraw(() => {

	const maskFunc = Math.floor(time()) % 2 === 0 ? drawSubtracted : drawMasked

	if (isKeyDown("space")) {
		maskFunc(() => {
			drawUVQuad({
				width: width(),
				height: height(),
				shader: "spiral",
				uniform: {
					"u_time": time(),
					"u_mpos": mousePos().scale(1 / width(), 1 / height()),
				},
			})
		}, drawStuff)
	} else {
		drawStuff()
	}

})

// It's a common practice to put all input handling and state updates before rendering.
onUpdate(() => {

	trail.push(mousePos())

	if (trail.length > 16) {
		trail.shift()
	}

	if (isMousePressed()) {
		doodles.push([])
	}

	if (isMouseDown() && isMouseMoved()) {
		doodles[doodles.length - 1].push(mousePos())
	}

})

```

As you can see here, It shows the groundwork for a simple rendering system in _kaboom.js_. Using the user's mouse. This is the full explanation for this code snippet;


First:

 * The script first tries to find the user's mouse in the window space.
   
 * then, script loads the following sprite for the mouse.

	* This just simply adds a shader to the mouse without using any inputs

```JS
loadShader("spiral", null, `
uniform float u_time;
uniform vec2 u_mpos;
vec4 frag(vec2 pos, vec2 uv, vec4 color, sampler2D tex) {
	vec2 pp = uv - u_mpos;
	float angle = atan(pp.y, pp.x);
	float dis = length(pp);
	float c = sin(dis * 48.0 + u_time * 8.0 + angle);
	return vec4(c, c, c, 1);
}
`)
```

Second:

 * After the script finds the user's mouse, it draws a trail that follows the user's mouse.

 * Once it's done with that, it waits until the user's clicks on something.

 * It takes that position, and draws an outline around that part of the screen where the user's clicks.

```JS
const t = (n = 1) => time() * n
const w = (a, b, n) => wave(a, b, t(n))
const px = 160
const py = 160
const doodles = []
const trail = []

const outline = {
	width: 4,
	color: rgb(0, 0, 0),
}

``` 

### Main Content

Moving on from the recap;

This learning log I haven't really learned a whole lot of stuff. It was mostly the same from last time. Such as: Last entry I stated that I thought it was complex overall. Also the fact that it has gotten easier than ever for the whole tinkering process. As well as the fact that in this code snippet it shows that what I was just talking about how I have learned is that _kaboom.js_ is much more simple than I thought. All shown here in this code snippet ;

```JS
// Kaboom as pure rendering lib (no component / game obj etc.)

kaboom()
loadSprite("bean", "/sprites/bean.png")

loadShader("spiral", null, `
uniform float u_time;
uniform vec2 u_mpos;
vec4 frag(vec2 pos, vec2 uv, vec4 color, sampler2D tex) {
	vec2 pp = uv - u_mpos;
	float angle = atan(pp.y, pp.x);
	float dis = length(pp);
	float c = sin(dis * 48.0 + u_time * 8.0 + angle);
	return vec4(c, c, c, 1);
}
`)

const t = (n = 1) => time() * n
const w = (a, b, n) => wave(a, b, t(n))
const px = 160
const py = 160
const doodles = []
const trail = []

const outline = {
	width: 4,
	color: rgb(0, 0, 0),
}

function drawStuff() {

	const mx = (width() - px * 2) / 2
	const my = (height() - py * 2) / 1
	const p = (x, y) => vec2(x, y).scale(mx, my).add(px, py)

	drawSprite({
		sprite: "bean",
		pos: p(0, 0),
		angle: t(40),
		anchor: "center",
		scale: w(1, 1.5, 4),
		color: rgb(w(128, 255, 4), w(128, 255, 8), 255),
	})

	drawRect({
		pos: p(1, 0),
		width: w(60, 120, 4),
		height: w(100, 140, 8),
		anchor: "center",
		radius: w(0, 32, 4),
		angle: t(80),
		color: rgb(w(128, 255, 4), 255, w(128, 255, 8)),
		outline,
	})

	drawEllipse({
		pos: p(2, 0),
		radiusX: w(40, 70, 2),
		radiusY: w(40, 70, 4),
		start: 0,
		end: w(180, 360, 1),
		color: rgb(255, w(128, 255, 8), w(128, 255, 4)),
		// gradient: [ Color.RED, Color.BLUE ],
		outline,
	})

	drawPolygon({
		pos: p(0, 1),
		pts: [
			vec2(w(-10, 10, 2), -80),
			vec2(80, w(-10, 10, 4)),
			vec2(w(30, 50, 4), 80),
			vec2(-30, w(50, 70, 2)),
			vec2(w(-50, -70, 4), 0),
		],
		colors: [
			rgb(w(128, 255, 8), 255, w(128, 255, 4)),
			rgb(255, w(128, 255, 8), w(128, 255, 4)),
			rgb(w(128, 255, 8), w(128, 255, 4), 255),
			rgb(255, 128, w(128, 255, 4)),
			rgb(w(128, 255, 8), w(128, 255, 4), 128),
		],
		outline,
	})

	drawText({
		text: "yo",
		pos: p(1, 1),
		anchor: "center",
		size: w(80, 120, 2),
		color: rgb(w(128, 255, 4), w(128, 255, 8), w(128, 255, 2)),
	})

	drawLines({
		...outline,
		pts: trail,
	})

	doodles.forEach((pts) => {
		drawLines({
			...outline,
			pts: pts,
		})
	})

}

// onDraw() is similar to onUpdate(), it runs every frame, but after all update events.
// All drawXXX() functions need to be called every frame if you want them to persist
onDraw(() => {

	const maskFunc = Math.floor(time()) % 2 === 0 ? drawSubtracted : drawMasked

	if (isKeyDown("space")) {
		maskFunc(() => {
			drawUVQuad({
				width: width(),
				height: height(),
				shader: "spiral",
				uniform: {
					"u_time": time(),
					"u_mpos": mousePos().scale(1 / width(), 1 / height()),
				},
			})
		}, drawStuff)
	} else {
		drawStuff()
	}

})

// It's a common practice to put all input handling and state updates before rendering.
onUpdate(() => {

	trail.push(mousePos())

	if (trail.length > 16) {
		trail.shift()
	}

	if (isMousePressed()) {
		doodles.push([])
	}

	if (isMouseDown() && isMouseMoved()) {
		doodles[doodles.length - 1].push(mousePos())
	}

})

```

As you can see here, It shows the groundwork for a simple rendering system in _kaboom.js_. Using the user's mouse. This is the full explanation for this code snippet;


First:

 * The script first tries to find the user's mouse in the window space.
   
 * then, script loads the following sprite for the mouse.

	* This just simply adds a shader to the mouse without using any inputs

```JS
loadShader("spiral", null, `
uniform float u_time;
uniform vec2 u_mpos;
vec4 frag(vec2 pos, vec2 uv, vec4 color, sampler2D tex) {
	vec2 pp = uv - u_mpos;
	float angle = atan(pp.y, pp.x);
	float dis = length(pp);
	float c = sin(dis * 48.0 + u_time * 8.0 + angle);
	return vec4(c, c, c, 1);
}
`)
```

Second:

 * After the script finds the user's mouse, it draws a trail that follows the user's mouse.

 * Once it's done with that, it waits until the user's clicks on something.

 * It takes that position, and draws an outline around that part of the screen where the user's clicks.

```JS
const t = (n = 1) => time() * n
const w = (a, b, n) => wave(a, b, t(n))
const px = 160
const py = 160
const doodles = []
const trail = []

const outline = {
	width: 4,
	color: rgb(0, 0, 0),
}

```

As well as the fact that I was mostly debugging a TON of my broken code that I didn't fix before hand in the last learning log. Such as in this learning log To start off my entry, I have been working on my freedom project _(FP)_. For quite a while. And I learned that _kaboom.js_ is more complex than I thought. So I started thinking with this tool. For this tool, _kaboom.js_ I started researching about _kaboom.js_. For this _FP_ project. And what I found out was that it's pretty easy for a simple baseline for _kaboom.js_.

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

This mainline code is for the base for your game. This might be because of the fact that _kaboom.js_ has the same syntax as _JS_ (Javascript). That's one reason why I believe _kaboom.js_ is so simple. This allows developers to easily change the starter code and create their own video games using the sandbox. 

### **_Challenges / Takeaways:_**

* My first challenge I faced was that I didn't tinker enough while working on _kaboom.js_ entry.  
	* _*because I keep getting errors, crashes, and plain issues._ 

* One BIG challenge I also had in my experience in this entry was that I didn't ask for help enough.
	* _Mostly because I didn't use slack or any other help._

* One takeaway I have was that I need to tinker more with my tool.
	* _Can because I can get a better knowing of what my tool can do._

* Another takeaway I got from this is that I need better time management skills in this space. 
	* _As in not doing everything last minute..._

* One last takeaway I have from this entry is that I would like to learn more from this point and knowing more about my tool will help me in the long run.


# **Section #7:**
### **Nayer Ebraheim - 1/6/25**

---

### Recap

In the last learning log I learned that, this learning log I haven't really learned a whole lot of stuff. It was mostly the same from last time. Such as: Last entry I stated that I thought it was complex overall. Also the fact that it has gotten easier than ever for the whole tinkering process. As well as the fact that in this code snippet it shows that what I was just talking about how I have learned is that _kaboom.js_ is much more simple than I thought. All shown here in this code snippet ;

```JS
// Kaboom as pure rendering lib (no component / game obj etc.)

kaboom()
loadSprite("bean", "/sprites/bean.png")

loadShader("spiral", null, `
uniform float u_time;
uniform vec2 u_mpos;
vec4 frag(vec2 pos, vec2 uv, vec4 color, sampler2D tex) {
	vec2 pp = uv - u_mpos;
	float angle = atan(pp.y, pp.x);
	float dis = length(pp);
	float c = sin(dis * 48.0 + u_time * 8.0 + angle);
	return vec4(c, c, c, 1);
}
`)

const t = (n = 1) => time() * n
const w = (a, b, n) => wave(a, b, t(n))
const px = 160
const py = 160
const doodles = []
const trail = []

const outline = {
	width: 4,
	color: rgb(0, 0, 0),
}

function drawStuff() {

	const mx = (width() - px * 2) / 2
	const my = (height() - py * 2) / 1
	const p = (x, y) => vec2(x, y).scale(mx, my).add(px, py)

	drawSprite({
		sprite: "bean",
		pos: p(0, 0),
		angle: t(40),
		anchor: "center",
		scale: w(1, 1.5, 4),
		color: rgb(w(128, 255, 4), w(128, 255, 8), 255),
	})

	drawRect({
		pos: p(1, 0),
		width: w(60, 120, 4),
		height: w(100, 140, 8),
		anchor: "center",
		radius: w(0, 32, 4),
		angle: t(80),
		color: rgb(w(128, 255, 4), 255, w(128, 255, 8)),
		outline,
	})

	drawEllipse({
		pos: p(2, 0),
		radiusX: w(40, 70, 2),
		radiusY: w(40, 70, 4),
		start: 0,
		end: w(180, 360, 1),
		color: rgb(255, w(128, 255, 8), w(128, 255, 4)),
		// gradient: [ Color.RED, Color.BLUE ],
		outline,
	})

	drawPolygon({
		pos: p(0, 1),
		pts: [
			vec2(w(-10, 10, 2), -80),
			vec2(80, w(-10, 10, 4)),
			vec2(w(30, 50, 4), 80),
			vec2(-30, w(50, 70, 2)),
			vec2(w(-50, -70, 4), 0),
		],
		colors: [
			rgb(w(128, 255, 8), 255, w(128, 255, 4)),
			rgb(255, w(128, 255, 8), w(128, 255, 4)),
			rgb(w(128, 255, 8), w(128, 255, 4), 255),
			rgb(255, 128, w(128, 255, 4)),
			rgb(w(128, 255, 8), w(128, 255, 4), 128),
		],
		outline,
	})

	drawText({
		text: "yo",
		pos: p(1, 1),
		anchor: "center",
		size: w(80, 120, 2),
		color: rgb(w(128, 255, 4), w(128, 255, 8), w(128, 255, 2)),
	})

	drawLines({
		...outline,
		pts: trail,
	})

	doodles.forEach((pts) => {
		drawLines({
			...outline,
			pts: pts,
		})
	})

}

// onDraw() is similar to onUpdate(), it runs every frame, but after all update events.
// All drawXXX() functions need to be called every frame if you want them to persist
onDraw(() => {

	const maskFunc = Math.floor(time()) % 2 === 0 ? drawSubtracted : drawMasked

	if (isKeyDown("space")) {
		maskFunc(() => {
			drawUVQuad({
				width: width(),
				height: height(),
				shader: "spiral",
				uniform: {
					"u_time": time(),
					"u_mpos": mousePos().scale(1 / width(), 1 / height()),
				},
			})
		}, drawStuff)
	} else {
		drawStuff()
	}

})

// It's a common practice to put all input handling and state updates before rendering.
onUpdate(() => {

	trail.push(mousePos())

	if (trail.length > 16) {
		trail.shift()
	}

	if (isMousePressed()) {
		doodles.push([])
	}

	if (isMouseDown() && isMouseMoved()) {
		doodles[doodles.length - 1].push(mousePos())
	}

})

```

As you can see here, It shows the groundwork for a simple rendering system in _kaboom.js_. Using the user's mouse. This is the full explanation for this code snippet;


First:

 * The script first tries to find the user's mouse in the window space.
   
 * then, script loads the following sprite for the mouse.

	* This just simply adds a shader to the mouse without using any inputs

```JS
loadShader("spiral", null, `
uniform float u_time;
uniform vec2 u_mpos;
vec4 frag(vec2 pos, vec2 uv, vec4 color, sampler2D tex) {
	vec2 pp = uv - u_mpos;
	float angle = atan(pp.y, pp.x);
	float dis = length(pp);
	float c = sin(dis * 48.0 + u_time * 8.0 + angle);
	return vec4(c, c, c, 1);
}
`)
```

Second:

 * After the script finds the user's mouse, it draws a trail that follows the user's mouse.

 * Once it's done with that, it waits until the user's clicks on something.

 * It takes that position, and draws an outline around that part of the screen where the user's clicks.

```JS
const t = (n = 1) => time() * n
const w = (a, b, n) => wave(a, b, t(n))
const px = 160
const py = 160
const doodles = []
const trail = []

const outline = {
	width: 4,
	color: rgb(0, 0, 0),
}

```

As well as the fact that I was mostly debugging a TON of my broken code that I didn't fix before hand in the last learning log. Such as in this learning log To start off my entry, I have been working on my freedom project _(FP)_. For quite a while. And I learned that _kaboom.js_ is more complex than I thought. So I started thinking with this tool. For this tool, _kaboom.js_ I started researching about _kaboom.js_. For this _FP_ project. And what I found out was that it's pretty easy for a simple baseline for _kaboom.js_.

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

This mainline code is for the base for your game. This might be because of the fact that _kaboom.js_ has the same syntax as _JS_ (Javascript). That's one reason why I believe _kaboom.js_ is so simple. This allows developers to easily change the starter code and create their own video games using the sandbox. 


### Main Content

Moving on from this recap;

The main content I have learned from this learning log is that making a shooter game is much easier than before. Like I said before hand; I have been learning _kaboom.js_ for a bit now. What I learned is that _kaboom.js_ has more depth than I thought. So I started thinking with this tool. With this tool, _kaboom.js_. I started researching about _kaboom.js_. For this _FP_ project. And what I found out was that it's pretty easy for a simple baseline for _kaboom.js_. Also, I wanted to add that it can also be because of the fact that _kaboom.js_ helps along with tools such as sprite spawning code and all that. With that being said, here is all what is shown in this code snippet; 

```JS
// TODO: document

kaboom({
	background: [74, 48, 82],
})

const objs = [
	"apple",
	"lightening",
	"coin",
	"egg",
	"key",
	"door",
	"meat",
	"mushroom",
]

for (const obj of objs) {
	loadSprite(obj, `/sprites/${obj}.png`)
}

loadBean()
loadSound("hit", "/examples/sounds/hit.mp3")
loadSound("shoot", "/examples/sounds/shoot.mp3")
loadSound("explode", "/examples/sounds/explode.mp3")
loadSound("OtherworldlyFoe", "/examples/sounds/OtherworldlyFoe.mp3")

scene("battle", () => {

	const BULLET_SPEED = 1200
	const TRASH_SPEED = 120
	const BOSS_SPEED = 48
	const PLAYER_SPEED = 480
	const STAR_SPEED = 120
	const BOSS_HEALTH = 1000
	const OBJ_HEALTH = 4

	const bossName = choose(objs)

	let insaneMode = false

	const music = play("OtherworldlyFoe")

	volume(0.5)

	function grow(rate) {
		return {
			update() {
				const n = rate * dt()
				this.scale.x += n
				this.scale.y += n
			},
		}
	}

	function late(t) {
		let timer = 0
		return {
			add() {
				this.hidden = true
			},
			update() {
				timer += dt()
				if (timer >= t) {
					this.hidden = false
				}
			},
		}
	}

	add([
		text("KILL", { size: 160 }),
		pos(width() / 2, height() / 2),
		anchor("center"),
		lifespan(1),
		fixed(),
	])

	add([
		text("THE", { size: 80 }),
		pos(width() / 2, height() / 2),
		anchor("center"),
		lifespan(2),
		late(1),
		fixed(),
	])

	add([
		text(bossName.toUpperCase(), { size: 120 }),
		pos(width() / 2, height() / 2),
		anchor("center"),
		lifespan(4),
		late(2),
		fixed(),
	])

	const sky = add([
		rect(width(), height()),
		color(0, 0, 0),
		opacity(0),
	])

	sky.onUpdate(() => {
		if (insaneMode) {
			const t = time() * 10
			sky.color.r = wave(127, 255, t)
			sky.color.g = wave(127, 255, t + 1)
			sky.color.b = wave(127, 255, t + 2)
			sky.opacity = 1
		} else {
			sky.color = rgb(0, 0, 0)
			sky.opacity = 0
		}
	})

	// 	add([
	// 		sprite("stars"),
	// 		scale(width() / 240, height() / 240),
	// 		pos(0, 0),
	// 		"stars",
	// 	])

	// 	add([
	// 		sprite("stars"),
	// 		scale(width() / 240, height() / 240),
	// 		pos(0, -height()),
	// 		"stars",
	// 	])

	// 	onUpdate("stars", (r) => {
	// 		r.move(0, STAR_SPEED * (insaneMode ? 10 : 1))
	// 		if (r.pos.y >= height()) {
	// 			r.pos.y -= height() * 2
	// 		}
	// 	})

	const player = add([
		sprite("bean"),
		area(),
		pos(width() / 2, height() - 64),
		anchor("center"),
	])

	onKeyDown("left", () => {
		player.move(-PLAYER_SPEED, 0)
		if (player.pos.x < 0) {
			player.pos.x = width()
		}
	})

	onKeyDown("right", () => {
		player.move(PLAYER_SPEED, 0)
		if (player.pos.x > width()) {
			player.pos.x = 0
		}
	})

	onKeyPress("up", () => {
		insaneMode = true
		music.speed = 2
	})

	onKeyRelease("up", () => {
		insaneMode = false
		music.speed = 1
	})

	player.onCollide("enemy", (e) => {
		destroy(e)
		destroy(player)
		shake(120)
		play("explode")
		music.detune = -1200
		addExplode(center(), 12, 120, 30)
		wait(1, () => {
			music.paused = true
			go("battle")
		})
	})

	function addExplode(p, n, rad, size) {
		for (let i = 0; i < n; i++) {
			wait(rand(n * 0.1), () => {
				for (let i = 0; i < 2; i++) {
					add([
						pos(p.add(rand(vec2(-rad), vec2(rad)))),
						rect(4, 4),
						scale(1 * size, 1 * size),
						lifespan(0.1),
						grow(rand(48, 72) * size),
						anchor("center"),
					])
				}
			})
		}
	}

	function spawnBullet(p) {
		add([
			rect(12, 48),
			area(),
			pos(p),
			anchor("center"),
			color(127, 127, 255),
			outline(4),
			move(UP, BULLET_SPEED),
			offscreen({ destroy: true }),
			// strings here means a tag
			"bullet",
		])
	}

	onUpdate("bullet", (b) => {
		if (insaneMode) {
			b.color = rand(rgb(0, 0, 0), rgb(255, 255, 255))
		}
	})

	onKeyPress("space", () => {
		spawnBullet(player.pos.sub(16, 0))
		spawnBullet(player.pos.add(16, 0))
		play("shoot", {
			volume: 0.3,
			detune: rand(-1200, 1200),
		})
	})

	function spawnTrash() {
		const name = choose(objs.filter(n => n != bossName))
		add([
			sprite(name),
			area(),
			pos(rand(0, width()), 0),
			health(OBJ_HEALTH),
			anchor("bot"),
			"trash",
			"enemy",
			{ speed: rand(TRASH_SPEED * 0.5, TRASH_SPEED * 1.5) },
		])
		wait(insaneMode ? 0.1 : 0.3, spawnTrash)
	}

	const boss = add([
		sprite(bossName),
		area(),
		pos(width() / 2, 40),
		health(BOSS_HEALTH),
		scale(3),
		anchor("top"),
		"enemy",
		{
			dir: 1,
		},
	])

	on("death", "enemy", (e) => {
		destroy(e)
		shake(2)
		addKaboom(e.pos)
	})

	on("hurt", "enemy", (e) => {
		shake(1)
		play("hit", {
			detune: rand(-1200, 1200),
			speed: rand(0.2, 2),
		})
	})

	const timer = add([
		text(0),
		pos(12, 32),
		fixed(),
		{ time: 0 },
	])

	timer.onUpdate(() => {
		timer.time += dt()
		timer.text = timer.time.toFixed(2)
	})

	onCollide("bullet", "enemy", (b, e) => {
		destroy(b)
		e.hurt(insaneMode ? 10 : 1)
		addExplode(b.pos, 1, 24, 1)
	})

	onUpdate("trash", (t) => {
		t.move(0, t.speed * (insaneMode ? 5 : 1))
		if (t.pos.y - t.height > height()) {
			destroy(t)
		}
	})

	boss.onUpdate((p) => {
		boss.move(BOSS_SPEED * boss.dir * (insaneMode ? 3 : 1), 0)
		if (boss.dir === 1 && boss.pos.x >= width() - 20) {
			boss.dir = -1
		}
		if (boss.dir === -1 && boss.pos.x <= 20) {
			boss.dir = 1
		}
	})

	boss.onHurt(() => {
		health bar.set(boss.hp())
	})

	boss.onDeath(() => {
		music.stop()
		go("win", {
			time: timer.time,
			boss: bossName,
		})
	})

	const health bar = add([
		rect(width(), 24),
		pos(0, 0),
		color(107, 201, 108),
		fixed(),
		{
			max: BOSS_HEALTH,
			set(hp) {
				this.width = width() * hp / this.max
				this.flash = true
			},
		},
	])

	healthbar.onUpdate(() => {
		if (health bar.flash) {
			health bar.color = rgb(255, 255, 255)
			health bar.flash = false
		} else {
			health bar.color = rgb(127, 255, 127)
		}
	})

	add([
		text("UP: insane mode", { width: width() / 2, size: 32 }),
		anchor("botleft"),
		pos(24, height() - 24),
	])

	spawnTrash()

})

scene("win", ({ time, boss }) => {

	const b = burp({
		loop: true,
	})

	loop(0.5, () => {
		b.detune = rand(-1200, 1200)
	})

	add([
		sprite(boss),
		color(255, 0, 0),
		anchor("center"),
		scale(8),
		pos(width() / 2, height() / 2),
	])

	add([
		text(time.toFixed(2), 24),
		anchor("center"),
		pos(width() / 2, height() / 2),
	])

})

go("battle")

```

This is the full code snippet, To show all the content in this. Here is the break-down;

First:

 * To start off, this section of the code,
   
 * All it does is start loading the assets into the space.
   
 * Such as _'BOSS SPEED'_ as well as _'BOSS HEALTH'_.
   
 * This sets the style of the game.

```JS
scene("battle", () => {

	const BULLET_SPEED = 1200
	const TRASH_SPEED = 120
	const BOSS_SPEED = 48
	const PLAYER_SPEED = 480
	const STAR_SPEED = 120
	const BOSS_HEALTH = 1000
	const OBJ_HEALTH = 4

	const bossName = choose(objs)

	let insaneMode = false

	const music = play("OtherworldlyFoe")

	volume(0.5)

	function grow(rate) {
		return {
			update() {
				const n = rate * dt()
				this.scale.x += n
				this.scale.y += n
			},
		}
	}
```

Second:

* The second thing that happens in this code snippet is the kill system for objects.
  
* This kill system for objects active when the player shots at the object in question.

```JS
function late(t) {
		let timer = 0
		return {
			add() {
				this.hidden = true
			},
			update() {
				timer += dt()
				if (timer >= t) {
					this.hidden = false
				}
			},
		}
	}

	add([
		text("KILL", { size: 160 }),
		pos(width() / 2, height() / 2),
		anchor("center"),
		lifespan(1),
		fixed(),
	])

	add([
		text("THE", { size: 80 }),
		pos(width() / 2, height() / 2),
		anchor("center"),
		lifespan(2),
		late(1),
		fixed(),
	])

	add([
		text(bossName.toUpperCase(), { size: 120 }),
		pos(width() / 2, height() / 2),
		anchor("center"),
		lifespan(4),
		late(2),
		fixed(),
	])
```

Third: 

* This section of the code is for keybinds.
  
* It first creates the player model then adds movement commands.
  
* Such as _'PLAYER_SPEED'_ left, up, down etc...
  
* Then there is a sub command when the player gets hit by an enemy using: _'destroy(player)'_.
  
```JS
const player = add([
		sprite("bean"),
		area(),
		pos(width() / 2, height() - 64),
		anchor("center"),
	])

	onKeyDown("left", () => {
		player.move(-PLAYER_SPEED, 0)
		if (player.pos.x < 0) {
			player.pos.x = width()
		}
	})

	onKeyDown("right", () => {
		player.move(PLAYER_SPEED, 0)
		if (player.pos.x > width()) {
			player.pos.x = 0
		}
	})

	onKeyPress("up", () => {
		insaneMode = true
		music.speed = 2
	})

	onKeyRelease("up", () => {
		insaneMode = false
		music.speed = 1
	})

	player.onCollide("enemy", (e) => {
		destroy(e)
		destroy(player)
		shake(120)
		play("explode")
		music.detune = -1200
		addExplode(center(), 12, 120, 30)
		wait(1, () => {
			music.paused = true
			go("battle")
		})
	})
```

Fourth:

* After that, it creates a death animation for the player with an explosion.
  
* with the function 'addExplode' it shows the death of the player with a distortion effect.

* The section of the code also creates a player bullet for the upcoming objects.

```JS
function addExplode(p, n, rad, size) {
		for (let i = 0; i < n; i++) {
			wait(rand(n * 0.1), () => {
				for (let i = 0; i < 2; i++) {
					add([
						pos(p.add(rand(vec2(-rad), vec2(rad)))),
						rect(4, 4),
						scale(1 * size, 1 * size),
						lifespan(0.1),
						grow(rand(48, 72) * size),
						anchor("center"),
					])
				}
			})
		}
	}

	function spawnBullet(p) {
		add([
			rect(12, 48),
			area(),
			pos(p),
			anchor("center"),
			color(127, 127, 255),
			outline(4),
			move(UP, BULLET_SPEED),
			offscreen({ destroy: true }),
			// strings here means a tag
			"bullet",
		])
	}

	onUpdate("bullet", (b) => {
		if (insaneMode) {
			b.color = rand(rgb(0, 0, 0), rgb(255, 255, 255))
		}
	})

	onKeyPress("space", () => {
		spawnBullet(player.pos.sub(16, 0))
		spawnBullet(player.pos.add(16, 0))
		play("shoot", {
			volume: 0.3,
			detune: rand(-1200, 1200),
		})
	})

```

Fifth:

* For the last thing on this code snippet, it adds a boss name with a health bar.
  
	* Using the _'bossName' && 'BOSS_HEALTH'_ tag for the boss.
   
* After that, it checks if the player's bullet hit the boss and decreases the _'BOSS_HEALTH'_ value.
  
* Once the boss dies, in the _'boss.onDeath()'_ it stops the game and plays a win sound effect. 

```JS
	function spawnTrash() {
		const name = choose(objs.filter(n => n != bossName))
		add([
			sprite(name),
			area(),
			pos(rand(0, width()), 0),
			health(OBJ_HEALTH),
			anchor("bot"),
			"trash",
			"enemy",
			{ speed: rand(TRASH_SPEED * 0.5, TRASH_SPEED * 1.5) },
		])
		wait(insaneMode ? 0.1 : 0.3, spawnTrash)
	}

	const boss = add([
		sprite(bossName),
		area(),
		pos(width() / 2, 40),
		health(BOSS_HEALTH),
		scale(3),
		anchor("top"),
		"enemy",
		{
			dir: 1,
		},
	])

	on("death", "enemy", (e) => {
		destroy(e)
		shake(2)
		addKaboom(e.pos)
	})

	on("hurt", "enemy", (e) => {
		shake(1)
		play("hit", {
			detune: rand(-1200, 1200),
			speed: rand(0.2, 2),
		})
	})

	const timer = add([
		text(0),
		pos(12, 32),
		fixed(),
		{ time: 0 },
	])

	timer.onUpdate(() => {
		timer.time += dt()
		timer.text = timer.time.toFixed(2)
	})

	onCollide("bullet", "enemy", (b, e) => {
		destroy(b)
		e.hurt(insaneMode ? 10 : 1)
		addExplode(b.pos, 1, 24, 1)
	})

	onUpdate("trash", (t) => {
		t.move(0, t.speed * (insaneMode ? 5 : 1))
		if (t.pos.y - t.height > height()) {
			destroy(t)
		}
	})

	boss.onUpdate((p) => {
		boss.move(BOSS_SPEED * boss.dir * (insaneMode ? 3 : 1), 0)
		if (boss.dir === 1 && boss.pos.x >= width() - 20) {
			boss.dir = -1
		}
		if (boss.dir === -1 && boss.pos.x <= 20) {
			boss.dir = 1
		}
	})

	boss.onHurt(() => {
		health bar.set(boss.hp())
	})

	boss.onDeath(() => {
		music.stop()
		go("win", {
			time: timer.time,
			boss: bossName,
		})
	})

	const healthbar = add([
		rect(width(), 24),
		pos(0, 0),
		color(107, 201, 108),
		fixed(),
		{
			max: BOSS_HEALTH,
			set(hp) {
				this.width = width() * hp / this.max
				this.flash = true
			},
		},
	])

	healthbar.onUpdate(() => {
		if (healthbar.flash) {
			healthbar.color = rgb(255, 255, 255)
			healthbar.flash = false
		} else {
			healthbar.color = rgb(127, 255, 127)
		}
	})

	add([
		text("UP: insane mode", { width: width() / 2, size: 32 }),
		anchor("botleft"),
		pos(24, height() - 24),
	])

	spawnTrash()

})

scene("win", ({ time, boss }) => {

	const b = burp({
		loop: true,
	})

	loop(0.5, () => {
		b.detune = rand(-1200, 1200)
	})

	add([
		sprite(boss),
		color(255, 0, 0),
		anchor("center"),
		scale(8),
		pos(width() / 2, height() / 2),
	])

	add([
		text(time.toFixed(2), 24),
		anchor("center"),
		pos(width() / 2, height() / 2),
	])

})

go("battle")
```

### **_Challenges / Takeaways:_**

* One challenge I had was the fact that I didn't know anything about kaboom.js when creating this entry.

	* This was before I started researching things about kaboom.js.

* Another challenge I faced was that I didn't tinker enough while working on kaboom.js entry.

	* because I keep getting errors, crashes, and plain issues.

* One other takeaway I had from this learning log was that I need better time management skills in this space.

	* As in not doing everything last minute...
   
* One takeaway I have was that I need to tinker more with my tool.
  
	* This is due to the fact that I can get a better understanding of what my tool can do.

# Section #8:
### Nayer Ebraheim - 2/20/25

### Main Content:

Starting off like all entries, I have been learning _kaboom.js_ for a bit now. What I learned is that _kaboom.js_ has more depth than I thought. So I started thinking with this tool. With this tool, _kaboom.js_. I started researching about _kaboom.js_. For this _FP_ project. And what I found out was that it's pretty easy for a simple baseline for _kaboom.js_. This entry I want to be different, instead of talking about the project I choose. I want to first explain what it is I'm talking about in the first place, second show the process in which I got to where I am. That's what I want to do. This is that example; 

```JS
// Start game
kaboom()

// Load assets
loadSprite("bean", "/sprites/bean.png")
loadSprite("coin", "/sprites/coin.png")
loadSprite("spike", "/sprites/spike.png")
loadSprite("grass", "/sprites/grass.png")
loadSprite("ghosty", "/sprites/ghosty.png")
loadSprite("portal", "/sprites/portal.png")
loadSound("score", "/examples/sounds/score.mp3")
loadSound("portal", "/examples/sounds/portal.mp3")

setGravity(2400)

const SPEED = 480

// Design 2 levels
const LEVELS = [
	[
		"@  ^ $$ >",
		"=========",
	],
	[
		"@   $   >",
		"=   =   =",
	],
]

// Define a scene called "game". The callback will be run when we go() to the scene
// Scenes can accept argument from go()
scene("game", ({ levelIdx, score }) => {

	// Use the level passed, or first level
	const level = addLevel(LEVELS[levelIdx || 0], {
		tileWidth: 64,
		tileHeight: 64,
		pos: vec2(100, 200),
		tiles: {
			"@": () => [
				sprite("bean"),
				area(),
				body(),
				anchor("bot"),
				"player",
			],
			"=": () => [
				sprite("grass"),
				area(),
				body({ isStatic: true }),
				anchor("bot"),
			],
			"$": () => [
				sprite("coin"),
				area(),
				anchor("bot"),
				"coin",
			],
			"^": () => [
				sprite("spike"),
				area(),
				anchor("bot"),
				"danger",
			],
			">": () => [
				sprite("portal"),
				area(),
				anchor("bot"),
				"portal",
			],
		},
	})

	// Get the player object from tag
	const player = level.get("player")[0]

	// Movements
	onKeyPress("space", () => {
		if (player.isGrounded()) {
			player.jump()
		}
	})

	onKeyDown("left", () => {
		player.move(-SPEED, 0)
	})

	onKeyDown("right", () => {
		player.move(SPEED, 0)
	})

	player.onCollide("danger", () => {
		player.pos = level.tile2Pos(0, 0)
		// Go to "lose" scene when we hit a "danger"
		go("lose")
	})

	player.onCollide("coin", (coin) => {
		destroy(coin)
		play("score")
		score++
		scoreLabel.text = score
	})

	// Fall death
	player.onUpdate(() => {
		if (player.pos.y >= 480) {
			go("lose")
		}
	})

	// Enter the next level on portal
	player.onCollide("portal", () => {
		play("portal")
		if (levelIdx < LEVELS.length - 1) {
			// If there's a next level, go() to the same scene but load the next level
			go("game", {
				levelIdx: levelIdx + 1,
				score: score,
			})
		} else {
			// Otherwise we have reached the end of the game, go to the "win" scene!
			go("win", { score: score })
		}
	})

	// Score counter text
	const scoreLabel = add([
		text(score),
		pos(12),
	])

})

scene("lose", () => {

	add([
		text("You Lose"),
		pos(12),
	])

	// Press any key to go back
	onKeyPress(start)

})

scene("win", ({ score }) => {

	add([
		text(`You grabbed ${score} coins!!!`, {
			width: width(),
		}),
		pos(12),
	])

	onKeyPress(start)

})

function start() {
	// Start with the "game" scene, with initial parameters
	go("game", {
		levelIdx: 0,
		score: 0,
	})
}

start()

```


### Content Process:

This _kaboom.js_ code is quite simple. First, what happens is that it loads all the assets. Then it sets the player's gravity and makes a movement system to that player. So that the player can smoothly move around the screen. As you can see in this code snippet, here;

Assets:

```JS
loadSprite("bean", "/sprites/bean.png")
loadSprite("coin", "/sprites/coin.png")
loadSprite("spike", "/sprites/spike.png")
loadSprite("grass", "/sprites/grass.png")
loadSprite("ghosty", "/sprites/ghosty.png")
loadSprite("portal", "/sprites/portal.png")
loadSound("score", "/examples/sounds/score.mp3")
loadSound("portal", "/examples/sounds/portal.mp3")

setGravity(2400)
const SPEED = 480
```

Movement:

```JS
	const player = level.get("player")[0]
	onKeyPress("space", () => {
		if (player.isGrounded()) {
			player.jump()
		}
	})
	onKeyDown("left", () => {
		player.move(-SPEED, 0)
	})
	onKeyDown("right", () => {
		player.move(SPEED, 0)
	})
```

After that is done it actually starts the game. With the player's movement system it creates a simple map with the fully loaded assets. Such as a kill part that makes the player restart. It also loads the portal assets that reports the player when they go over it. As shown here in this code snippet; 

```JS
scene("game", ({ levelIdx, score }) => {
	const level = addLevel(LEVELS[levelIdx || 0], {
		tileWidth: 64,
		tileHeight: 64,
		pos: vec2(100, 200),
		tiles: {
			"@": () => [
				sprite("bean"),
				area(),
				body(),
				anchor("bot"),
				"player",
			],
			"=": () => [
				sprite("grass"),
				area(),
				body({ isStatic: true }),
				anchor("bot"),
			],
			"$": () => [
				sprite("coin"),
				area(),
				anchor("bot"),
				"coin",
			],
			"^": () => [
				sprite("spike"),
				area(),
				anchor("bot"),
				"danger",
			],
			">": () => [
				sprite("portal"),
				area(),
				anchor("bot"),
				"portal",
			],
		},
	})

```

Finally, the last part of the code is a bit more complex than the rest of the code. This part of the code listens for when the player collides with an object. Such as a spike or a coin. If the player collides with a spike - it's known as a "danger" class. - The player will just simply die and get the lost screen (the whole game resets). Now, if the player collides with a coin - it will just add points to the player's score. At the very end of the level, there is a portal. If the player goes through this portal, they get transported to the next level. All of this code is shown here in this code snippet;       

Player Collides + Scoring:

```JS
player.onCollide("danger", () => {
		player.pos = level.tile2Pos(0, 0)
		// Go to "lose" scene when we hit a "danger"
		go("lose")
	})

	player.onCollide("coin", (coin) => {
		destroy(coin)
		play("score")
		score++
		scoreLabel.text = score
	})

	player.onUpdate(() => {
		if (player.pos.y >= 480) {
			go("lose")
		}
	})

	player.onCollide("portal", () => {
		play("portal")
		if (levelIdx < LEVELS.length - 1) {
			// If there's a next level, go() to the same scene but load the next level
			go("game", {
				levelIdx: levelIdx + 1,
				score: score,
			})
		} else {
			// Otherwise we have reached the end of the game, go to the "win" scene!
			go("win", { score: score })
		}
	})


	const scoreLabel = add([
		text(score),
		pos(12),
	])
```

### **_Challenges / Takeaways:_**

* Another challenge I faced was that I didn't tinker enough while working on kaboom.js entry.

	* because I keep getting errors, crashes, and plain issues.
   
 	* This was before I started researching things about kaboom.js.

* One other takeaway I had from this learning log was that I need better time management skills in this space.

	* As in not doing everything last minute...
   
* One takeaway I have was that I need to tinker more with my tool.
  
	* This is due to the fact that I can get a better understanding of what my tool can do.



# Section #9:
### Nayer Ebraheim - 3/10/25

### Main Content:

Like every section, at first I started off by googling "how do mouse positions work in kaboom.js?" Which didn't get me anything at first. But I did some digging, and I found out how to use it in my platformer game. This led me into a rabbit hole I didn't even know was possible. But I got through it and found a way to create user mouse inputs. This code shown below shows the user mouse inputs used in a maze type game. Here is the code for this:        

```JS
kaboom({
	scale: 0.5,
	background: [0, 0, 0],
})

loadSprite("bean", "sprites/bean.png")
loadSprite("steel", "sprites/steel.png")

const TILE_WIDTH = 64
const TILE_HEIGHT = TILE_WIDTH

function createMazeMap(width, height) {
	const size = width * height
	function getUnvisitedNeighbours(map, index) {
		const n = []
		const x = Math.floor(index / width)
		if (x > 1 && map[index - 2] === 2) n.push(index - 2)
		if (x < width - 2 && map[index + 2] === 2) n.push(index + 2)
		if (index >= 2 * width && map[index - 2 * width] === 2) n.push(index - 2 * width)
		if (index < size - 2 * width && map[index + 2 * width] === 2) n.push(index + 2 * width)
		return n
	}
	const map = new Array(size).fill(1, 0, size)
	map.forEach((_, index) => {
		const x = Math.floor(index / width)
		const y = Math.floor(index % width)
		if ((x & 1) === 1 && (y & 1) === 1) {
			map[index] = 2
		}
	})

	const stack = []
	const startX = Math.floor(Math.random() * (width - 1)) | 1
	const startY = Math.floor(Math.random() * (height - 1)) | 1
	const start = startX + startY * width
	map[start] = 0
	stack.push(start)
	while (stack.length) {
		const index = stack.pop()
		const neighbours = getUnvisitedNeighbours(map, index)
		if (neighbours.length > 0) {
			stack.push(index)
			const neighbour = neighbours[Math.floor(neighbours.length * Math.random())]
			const between = (index + neighbour) / 2
			map[neighbour] = 0
			map[between] = 0
			stack.push(neighbour)
		}
	}
	return map
}

function createMazeLevelMap(width, height, options) {
	const symbols = options?.symbols || {}
	const map = createMazeMap(width, height)
	const space = symbols[" "] || " "
	const fence = symbols["#"] || "#"
	const detail = [
		space,
		symbols["╸"] || "╸",   //  1
		symbols["╹"] || "╹",   //  2
		symbols["┛"] || "┛",   //  3
		symbols["╺"] || "╺",   //  4
		symbols["━"] || "━",   //  5
		symbols["┗"] || "┗",   //  6
		symbols["┻"] || "┻",   //  7
		symbols["╻"] || "╻",   //  8
		symbols["┓"] || "┓",   //  9
		symbols["┃"] || "┃",   //  a
		symbols["┫"] || "┫",   //  b
		symbols["┏"] || "┏",   //  c
		symbols["┳"] || "┳",   //  d
		symbols["┣"] || "┣",   //  e
		symbols["╋ "] || "╋ ", //  f
	]
	const symbolMap = options?.detailed ? map.map((s, index) => {
		if (s === 0) return space
		const x = Math.floor(index % width)
		const leftWall = x > 0 && map[index - 1] == 1 ? 1 : 0
		const rightWall = x < width - 1 && map[index + 1] == 1 ? 4 : 0
		const topWall = index >= width && map[index - width] == 1 ? 2 : 0
		const bottomWall = index < height * width - width && map[index + width] == 1 ? 8 : 0
		return detail[leftWall | rightWall | topWall | bottomWall]
	}) : map.map((s) => {
		return s == 1 ? fence : space
	})
	const levelMap = []
	for (let i = 0; i < height; i++) {
		levelMap.push(symbolMap.slice(i * width, i * width + width).join(""))
	}
	return levelMap
}

const level = addLevel(
	createMazeLevelMap(15, 15, {}),
	{
		tileWidth: TILE_WIDTH,
		tileHeight: TILE_HEIGHT,
		tiles: {
			"#": () => [
				sprite("steel"),
				tile({ isObstacle: true }),
			],
		},
	},
)

const bean = level.spawn([
	sprite("bean"),
	anchor("center"),
	pos(32, 32),
	tile(),
	agent({ speed: 640, allowDiagonals: true }),
	"bean",
], 1, 1)

onClick(() => {
	const pos = mousePos()
	bean.setTarget(vec2(
		Math.floor(pos.x / TILE_WIDTH) * TILE_WIDTH + TILE_WIDTH / 2,
		Math.floor(pos.y / TILE_HEIGHT) * TILE_HEIGHT + TILE_HEIGHT / 2,
	))
})
```

### Content Process:

As you can see, this code is actually pretty simple at first glance. First, it creates an asset with the width valve set to 64 in `const TILE_WIDTH = 64,` and with `const TILE_HEIGHT = TILE_WIDTH`. Then it randomizes the assets placements with the function `.createMazeMap`. In this function, it gets the `index`, and pushes the `index` valve. After that it returns the map layout with `stack.push(neighbour), return map`. As shown below;


**Tile Width + Maze Matrix Randomization:**
```JS
const TILE_WIDTH = 64
const TILE_HEIGHT = TILE_WIDTH

function createMazeMap(width, height) {
	const size = width * height
	function getUnvisitedNeighbours(map, index) {
		const n = []
		const x = Math.floor(index / width)
		if (x > 1 && map[index - 2] === 2) n.push(index - 2)
		if (x < width - 2 && map[index + 2] === 2) n.push(index + 2)
		if (index >= 2 * width && map[index - 2 * width] === 2) n.push(index - 2 * width)
		if (index < size - 2 * width && map[index + 2 * width] === 2) n.push(index + 2 * width)
		return n
	}
	const map = new Array(size).fill(1, 0, size)
	map.forEach((_, index) => {
		const x = Math.floor(index / width)
		const y = Math.floor(index % width)
		if ((x & 1) === 1 && (y & 1) === 1) {
			map[index] = 2
		}
	})

	const stack = []
	const startX = Math.floor(Math.random() * (width - 1)) | 1
	const startY = Math.floor(Math.random() * (height - 1)) | 1
	const start = startX + startY * width
	map[start] = 0
	stack.push(start)
	while (stack.length) {
		const index = stack.pop()
		const neighbours = getUnvisitedNeighbours(map, index)
		if (neighbours.length > 0) {
			stack.push(index)
			const neighbour = neighbours[Math.floor(neighbours.length * Math.random())]
			const between = (index + neighbour) / 2
			map[neighbour] = 0
			map[between] = 0
			stack.push(neighbour)
		}
	}
	return map
}
```

Second, once it's done with that, it actually louds the maze in the window with `const symbols = options?.symbols || {}, const map = createMazeMap(width, height), const space = symbols[" "] || " ", const fence = symbols["#"] || "#"`. Using this, will print it onto the screen. Once that is done, it returns the space using the function `symbolMap`. It uses the math matrix to print out the maze onto the screen. With the help of the `const detail`. After all of that is done it returns the valve of `levelMap` as shown here;  


**Maze Metrix Printing:**
```JS
function createMazeLevelMap(width, height, options) {
	const symbols = options?.symbols || {}
	const map = createMazeMap(width, height)
	const space = symbols[" "] || " "
	const fence = symbols["#"] || "#"
	const detail = [
		space,
		symbols["╸"] || "╸",   //  1
		symbols["╹"] || "╹",   //  2
		symbols["┛"] || "┛",   //  3
		symbols["╺"] || "╺",   //  4
		symbols["━"] || "━",   //  5
		symbols["┗"] || "┗",   //  6
		symbols["┻"] || "┻",   //  7
		symbols["╻"] || "╻",   //  8
		symbols["┓"] || "┓",   //  9
		symbols["┃"] || "┃",   //  a
		symbols["┫"] || "┫",   //  b
		symbols["┏"] || "┏",   //  c
		symbols["┳"] || "┳",   //  d
		symbols["┣"] || "┣",   //  e
		symbols["╋ "] || "╋ ", //  f
	]
	const symbolMap = options?.detailed ? map.map((s, index) => {
		if (s === 0) return space
		const x = Math.floor(index % width)
		const leftWall = x > 0 && map[index - 1] == 1 ? 1 : 0
		const rightWall = x < width - 1 && map[index + 1] == 1 ? 4 : 0
		const topWall = index >= width && map[index - width] == 1 ? 2 : 0
		const bottomWall = index < height * width - width && map[index + width] == 1 ? 8 : 0
		return detail[leftWall | rightWall | topWall | bottomWall]
	}) : map.map((s) => {
		return s == 1 ? fence : space
	})
	const levelMap = []
	for (let i = 0; i < height; i++) {
		levelMap.push(symbolMap.slice(i * width, i * width + width).join(""))
	}
	return levelMap
}
```

Finally, the last thing on the list is this. This is what makes the player move around the maze. Using the `level.spawn` addon, it allows the player to move around with a set speed, height, and size. Now to make the player actually move using the user mouse inputs. It can be used by the `onClick` type with `const pos = mousePos(), bean.setTarget(vec2`. It first listens for the user mouse click. Then it gets the position relative to the window space. After that it creates a path to the mouse so that the assets (player) can go to that position. As shown in this code piece.    

**Player Movement + User Mouse Input:**
```JS
const level = addLevel(
	createMazeLevelMap(15, 15, {}),
	{
		tileWidth: TILE_WIDTH,
		tileHeight: TILE_HEIGHT,
		tiles: {
			"#": () => [
				sprite("steel"),
				tile({ isObstacle: true }),
			],
		},
	},
)

const bean = level.spawn([
	sprite("bean"),
	anchor("center"),
	pos(32, 32),
	tile(),
	agent({ speed: 640, allowDiagonals: true }),
	"bean",
], 1, 1)

onClick(() => {
	const pos = mousePos()
	bean.setTarget(vec2(
		Math.floor(pos.x / TILE_WIDTH) * TILE_WIDTH + TILE_WIDTH / 2,
		Math.floor(pos.y / TILE_HEIGHT) * TILE_HEIGHT + TILE_HEIGHT / 2,
	))
})
```

### **_Challenges / Takeaways:_**

* I don't have a lot of changes for this log but I believe that One challenge I had while writing up this learning log was that I felt that I had forgotten a bit of kaboom.js over the weeks.

	* This might have been because I had stopped tinkering for a bit of time while doing other things. 

* Another challenge I faced was that I didn't tinker enough while working on kaboom.js entry.

	* because I keep getting errors, crashes, and plain issues.
   
 	* This was before I started researching things about kaboom.js.

* One other takeaway I had from this learning log was that I need better time management skills in this space.

	* As in not doing everything last minute.

* Another takeaway I got from this is that these logs shouldn't be tossed away and forgotten.
  
	* Instead it should be done right in this context.
   
* One takeaway I have was that I need to tinker more with my tool.
  
	* This is due to the fact that I can get a better understanding of what my tool can do.





# Section #10:
### Nayer Ebraheim - 3/17/25


### Main Content:


For some context; like every log I have done so far, I believe that over the months that actually I have learned a ton over these mouths. This is why I researched things before I started this learning log. To get a better feel for how I'm going to layout the whole document for this project that's connected to kaboom.js. That is why I did it to get a better understanding of the js framework. I started researching the framework because I mostly forgot the framework itself. Since I had completely stopped researching the parts I needed for the project. 


### Content Process:


Moving on to the content process; I have done a lot of research over the mouth like I said before, To get a better feel for how I'm going to layout the whole document for this project that's connected to kaboom.js. for the major reason of understanding the framework better. That's simply why I did it. Now, this research I kept trying to find more things to use for my project, even if it was small things. Such as having an object follow your mouse or even a moving ball that gets destroyed if it collides with the borders of the window. With just these two things you have yourself a game of pong. As shown in the preview bellow in this;   

**Preview:**

```JS
kaboom({
	background: [ 255, 100, 128 ],
})

 // adding all phy obj into the window
	add([
		pos(40, 0),
		rect(20, 180),
		outline(15),
		anchor("center"),
		area(),
		"slider",
	])
	
	add([
		pos(width() - 40, 0),
		rect(20, 180),
		outline(15),
		anchor("center"),
		area(),
		"slider",
	])
	
	// this moves the slides with mouse
	onUpdate("slider", (p) => {
		p.pos.y = mousePos().y
	})
	
	// score settings: 
	let score = 0
	
	add([
		text(score),
		pos(center()),
		anchor("center"),
		z(50),
		{ update() { this.text = score }},
	])
	
	// ball
	let speed = 500
	
	const ball = add([
		pos(center()),
		circle(30),
		outline(40,255),

		area({ shape: new Rect(vec2(-16), 32, 32) }),
		{ vel: Vec2.fromAngle(rand(-20, 20)) },
	])

	
	// if the ball hits the floor, it bounds back:

	ball.onUpdate(() => {
		ball.move(ball.vel.scale(speed))
		if (ball.pos.x < 0 || ball.pos.x > width()) {
			score = 0
			ball.pos = center()
			ball.vel = Vec2.fromAngle(rand(-20, 20))
			speed = 500
		}
		if (ball.pos.y < 0 || ball.pos.y > height()) {
			ball.vel.y = -ball.vel.y
		}
	})
	
	//  With this, the ball should be able to bound of the sliders 
	ball.onCollide("slider", (p) => {
		speed += 100
		ball.vel = Vec2.fromAngle(ball.pos.angle(p.pos))
		score++
	})
console.log(score++)

```

Now before I get into the explanation, like I said before; _this research I kept trying to find more things to use for my project, even if it was small things. Such as having an object follow your mouse or even a moving ball that gets destroyed if it collides with the borders of the window._ I said this because of the fact that I tried keeping the code simple so that it would be easier for me to use it in my game. The first thing in the preview that happens is spawning the both sliders as well as the scoring system. Using the `add` _tag_ it will _(you guessed it)_ add objects into the playable window. After that, the sliders get linked to the mouse. So that if the mouse moves up, the sliders move up _(and vice versa.)_ Now onto the scoring system, all it does is that it detects when one of the sliders has been hit. Using `let score = 0, add([ text(score), pos(center()), anchor("center"), z(50), { update() { this.text = score }}, ])` If one of the sliders were to be hit by the ball, it just adds a point to the score. All of the code is shown below;     


**Adding Objects + Scoring System:**

```JS
// adding all phy obj into the window
	add([
		pos(40, 0),
		rect(20, 180),
		outline(15),
		anchor("center"),
		area(),
		"slide",
	])
	
	add([
		pos(width() - 40, 0),
		rect(20, 180),
		outline(15),
		anchor("center"),
		area(),
		"slide",
	])
	
	// this moves the slides with mouse
	onUpdate("slide", (p) => {
		p.pos.y = mousePos().y
	})
	
	// score settings: 
	let score = 0
	
	add([
		text(score),
		pos(center()),
		anchor("center"),
		z(50),
		{ update() { this.text = score }},
	])
```

The second part of this code that happens is the playable physics ball that bounds off the moving sliders once it hits the sliders. How this works is actually pretty simple, it first creates the ball with a set speed using this; `let speed = 500,` as well as `const ball = add([ pos(center()), circle(30), outline(40,255), area({ shape: new Rect(vec2(-16), 32, 32) }), { vel: Vec2.fromAngle(rand(-20, 20)) }, ])`JS  

**Ball Physics + Window Borders:** 

```JS
	// ball
	let speed = 500
	
	const ball = add([
		pos(center()),
		circle(30),
		outline(40,255),

		area({ shape: new Rect(vec2(-16), 32, 32) }),
		{ vel: Vec2.fromAngle(rand(-20, 20)) },
	])

	
	// if the ball hits the floor, it bounds back:

	ball.onUpdate(() => {
		ball.move(ball.vel.scale(speed))
		if (ball.pos.x < 0 || ball.pos.x > width()) {
			score = 0
			ball.pos = center()
			ball.vel = Vec2.fromAngle(rand(-20, 20))
			speed = 500
		}
		if (ball.pos.y < 0 || ball.pos.y > height()) {
			ball.vel.y = -ball.vel.y
		}
	})
	
	//  With this, the ball should be able to bound of the sliders 
	ball.onCollide("slide", (p) => {
		speed += 100
		ball.vel = Vec2.fromAngle(ball.pos.angle(p.pos))
		score++
	})
console.log(score++)
```

### **_Challenges / Takeaways:_**


* One challenge I faced was that I didn't tinker enough while working on kaboom.js entry.


























---




#### **__Links:__**


* [**_SEP11_ Notes**](https://docs.google.com/document/d/1gly2fPFv1kMC2Yhh0gaxdvVpJllQ7ZP_zgr2o0gLdO0/edit?tab=t.0#heading=h.13rj6cmkj5s0)

    * [JS-bin:](https://jsbin.com/?js,output)

    * [Code Samples:](https://docs.google.com/document/d/1-mfcBeP-TTHBOmmZzVRX3DUNUvo62zFFuJiPtEnw_aw/edit)

* [**The Full Students Repo!**](https://github.com/hstatsep-students/)

* [**Back-up File**]([https://github.com/hstatsep-students/](https://docs.google.com/document/d/1yrKNAZdbyX2kILznwNRwCuQ5KQs6V8Or137lMeWlbgc/edit?tab=t.0))


---



























---




**(End of Page)**


