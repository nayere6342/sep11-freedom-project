# Entry 1
## **Nayer Ebraheim - 10/28/24**

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

This might be because of the fact that

[Next](entry02.md)

[Home](../README.md)

