# **Entry 5:**
### **Nayer Ebraheim - 12/2/24**

---

For starters, this log was fun, I have learned a whole lot about _kaboom.js_. Such as how to create a player event call. This allows parts of the game to react to the environment. That one task in the game can make the whole game feel more alive in this context. But, before I get into that, a recap is need from the last learning log,

## Recap:

**This is the recap for _(entry #4)_**

One thing I have learned throughout this entry process is that. One like I said before I have learned is that kaboom.js is much more simple than I thought. Last entry I stated that I thought it was complex overall. This was because I didn't really know how kaboom.js work to begin with. As well as the fact that it has gotten easier than ever for the whole tinkering process. This can be because of the simple fact that I have more knowledge on this subject of my tool kaboom.js As shown here in this code snippet:

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

Moving on,

This is all the things that I have learn with this entry. In _entry #4_ I have learned that,

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
