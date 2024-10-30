# **Entry 1:**
### **Nayer Ebraheim - 10/28/24**

---

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

This mainline code is for the base for your game. This might be because of the fact that _kaboom.js_ has the same syntax as _JS_ (Javascript). That's one reason why I believe _kaboom.js_ is so simple. This allows developers to easily change the starter code and create their own video games using the sandbox. The sandbox is where developers can try new things for their games. This is a very useful tool for _kaboom.js_ as a whole. That's what I believe overall... I also think that _kaboom.js_ can be used for collision handling. This is great for giving life throughout your game overall. 
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



