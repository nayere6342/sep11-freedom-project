# **Entry 5:**
### **Nayer Ebraheim - 4/21/25**

---

## **Intro:**

In this entry, I will be going over the code process of the topic that I'll be presenting. Like before, I'll explain the topic by breaking it down into smaller bite size pieces so that it can be looked at easier. But this time, I will be presenting the parts of the code I have used in my MVP project. My MVP project was about a platformer, a platformer about running and about how I spend those entries to help me. I also used these entries for notes. So that I can use them in the future. Like for the game I'm developing for this platformer project.   

## **Code Presentation:**

Since this is the code I what have been working on for my platformer game for the last week, it will be harder to explain the code moving on since I'm still activly working on the platformer project.       

```JS
<script src="main.js"></script>

	<script type="module">
	import kaboom from "https://unpkg.com/kaboom@3000.0.1/dist/kaboom.mjs";
		// Collision handling

			// Start kaboom
			kaboom({
				background: [88,188,132]
			})

			// Load assets
			loadSprite("player", "/sprites/8bit-player.png")
			loadSprite("coin", "/sprites/8bit-coin.png")
			loadSprite("base", "/sprites/8bit-baseline.png")
			loadSprite("plate", "/sprites/8bit-plateform.png")
			loadSprite("fire", "/sprites/8bit-fire.png")
			loadSprite("backdrop", "/sprites/8bit-backdrop.png")



			const SPEED = 520
			setGravity(1600)

			const score = add([
			text("Score:"),
			pos(24, 24),
			{ value: 0 },
		])




			const player = add([
				sprite("player"),
				scale(.1),
				pos(100, 100),
				color(),
				rotate(0),
				area(),
				body(),
			])


			onKeyDown("a", () => {
				player.move(-SPEED, 0)
			})

			onKeyDown("d", () => {
				player.move(SPEED, 0)
			})

			onKeyPress("space", () => {
			if (player.isGrounded()) {
				player.jump()
			}
		})

			onKeyDown("s", () => {
				player.move(0, SPEED)
			})




			// for (let i = 0; i < 3; i++) {

			// 	const x = rand(+20, width())
			// 	const y = rand(+10, height())
			// 	const z = rand(+30, height())
			// 	const i = rand(+30, height())

			// 	add([
			// 		sprite("coin"),
			// 		scale(.2),
			// 		pos(x,y,z,i),
			// 		area(),
			// 		"coin",
			// 	])

			// }




			function spawnMap() {
				add([
					sprite("base"),
					scale(.3),
					area(),
					body({ isStatic: true }),
					pos(width(), height() - 10),
					anchor("botleft"),
					move(LEFT, 540),
					"base",
				]);
				wait(rand(.2, .2), () => {
					spawnMap();
				});
			}

				spawnMap();
				add([
				sprite("plate"),
				scale(.2),
				pos(40,220),
				area(),
				body({ isStatic: true }),
				"plate",
			])



			function spawnDead() {
				add([
					sprite("fire"),
					scale(.1),
					area(),
					body({ isStatic: true }),
					pos(width(), height() - 200),
					anchor("botleft"),
					move(LEFT, 540),
					"fire",
				]);
				wait(rand(2, 1.8), () => {
					spawnDead();
				});
			}

				spawnDead();
				add([
				sprite("fire"),
				scale(.4),
				pos(500,-2000),
				area(),
				body({ isStatic: true }),
				"fire",
			])

			function spawnScore() {
				add([
					sprite("coin"),
					scale(.1),
					area(),
					body({ isStatic: true }),
					pos(width(), height() - 220),
					anchor("botleft"),
					move(LEFT, 440),
					"coin",
				]);
				wait(rand(3, 3), () => {
					spawnScore();
					// destroy(grass)
				});
			}

				spawnScore();
				add([
				sprite("coin"),
				scale(.4),
				pos(500,-2000),
				area(),
				body({ isStatic: true }),
				"coin",
			])










			player.onCollide("fire", (enemy) => {
				destroy(player)
				const die = add([
			text("[ GAME OVER ]"),
			pos(500,200),
		])

			})


			player.onCollideUpdate("fire'", (fire) => {
				destroyAll(fire)
			})

			player.onCollide("coin", (enemy) => {
				score.value += 1
				score.text = "Score:" + score.value


			})


			player.onCollideUpdate("coin", (coin) => {
				destroy(coin)
			})
```


### **Design Process:** 

For this design process

### **Challenges & Takeaways:**

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





