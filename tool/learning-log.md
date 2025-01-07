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
This code is from _kaboom.js_ code snippts. All the code does is:

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

* One challenge I had was that felt like whole lot of time to really tinker with this entry.

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

 * Once it's done with that, it waits until the the user's clicks on something.

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
    
  * Also this might because of the fact that tt's pretty common to put all input handling and state updates before rendering begains.
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

* One challenge I had was that felt like whole lot of time to really tinker with this entry.

* One challenge I had was the fact that I didn't know anything about _kaboom.js_ when creating this entry.
  
	* _This was before I started researching things about kaboom.js._ 

* Another challenge I faced was that I didn't tinker enough while working on _kaboom.js_ entry.
  
	* _*because I keep getting errors, crashes, and plain issues._
   

* One takeaway I have was that I need to tinker more with my tool.
  
	* _Can because I can get a better knowing of what my tool can do._

* One other takeaway I a had from this learning log was that I need better time management skills in this space.
  
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

 * Once it's done with that, it waits until the the user's clicks on something.

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

this learning log I haven't really learn a whole lot of stuff It's was mosty the same from last time. Such as: Last entry I stated that I thought it was complex overall. Also the fact that it has gotten easier than ever for the whole tinkering process. As well as the fact that in this code snippet it shows that what I was just talking about how I have learned is that _kaboom.js_ is much more simple than I thought. All shown here in this code snippect ;

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

 * Once it's done with that, it waits until the the user's clicks on something.

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

In the last learning log I learn that, this learning log I haven't really learn a whole lot of stuff It's was mosty the same from last time. Such as: Last entry I stated that I thought it was complex overall. Also the fact that it has gotten easier than ever for the whole tinkering process. As well as the fact that in this code snippet it shows that what I was just talking about how I have learned is that _kaboom.js_ is much more simple than I thought. All shown here in this code snippect ;

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

 * Once it's done with that, it waits until the the user's clicks on something.

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

The main content I have learn from this learning log is that making a shooter game is much more easier then before. This can also be because of the fact that _kaboom.js_ helps along with tools such as sprite sponwing code. All of this is shown in this code snippent; 

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
		healthbar.set(boss.hp())
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

First:

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
		healthbar.set(boss.hp())
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


* One other takeaway I a had from this learning log was that I need better time management skills in this space.

	* As in not doing everything last minute...
   
* One takeaway I have was that I need to tinker more with my tool.
  
	* This is due to the fact that I can get a better knowing of what my tool can do.



---




#### **__Links:__**


* [**_SEP11_ Notes**](https://docs.google.com/document/d/1Q65VlDlA7tV9fuXP8XF-dxXL7TYyunfjoUTJqYvnTyI/edit)

    * [JS-bin](https://jsbin.com/?js,output)

    * [Code Samples](https://docs.google.com/document/d/1-mfcBeP-TTHBOmmZzVRX3DUNUvo62zFFuJiPtEnw_aw/edit)

* [**The Full Students Repo**](https://github.com/hstatsep-students/)

    * [_SEP11_ Blog Page Overview](https://github.com/nayere6342/sep11-freedom-project/tree/main)

    * [_SEP11_ Entry Backup Doc](https://docs.google.com/document/d/1YnfwCAMGU6oB6WFvHh2m1oW8arNfXkU21ra7zhBruOA/edit?tab=t.0)




---

