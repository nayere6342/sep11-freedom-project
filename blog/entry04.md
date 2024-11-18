# **Entry 4:**
### **Nayer Ebraheim - 11/18/24**

---

To start off my entry, I have been working on my freedom project (FP). For a long time by this point. And, what I have learned is that _kaboom.js_ is much more simple than I thought. Last entry I stated that I thought it was complexed overall. This was because I didn't really know how _kaboom.js_ work to begain with. Before I get into that, I will show you a quick recap!

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
 

---


#### **__Links:__**


* [**_SEP11_ Notes**](https://docs.google.com/document/d/1Q65VlDlA7tV9fuXP8XF-dxXL7TYyunfjoUTJqYvnTyI/edit)

    * [**JS-bin**](https://jsbin.com/?js,output)

* [**The Full Students Repo**](https://github.com/hstatsep-students/)

    * [**_SEP11_Blog Page Overview**](https://github.com/nayere6342/sep11-freedom-project/tree/main)

    * [**_SEP11_Entry Backup Doc**](https://docs.google.com/document/d/1YnfwCAMGU6oB6WFvHh2m1oW8arNfXkU21ra7zhBruOA/edit?tab=t.0)


---

[Previous](entry01.md) | [Next Entry](entry03.md)

[Back Home](../README.md)
