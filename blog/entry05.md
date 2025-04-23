# **Entry 5:**
### **Nayer Ebraheim - 4/21/25**

---

## **Intro:**

In this entry, I will be going over the code process of the topic that I'll be presenting. Like before, I'll explain the topic by breaking it down into smaller bite size pieces so that it can be looked at easier. But this time, I will be presenting the parts of the code I have used in my MVP project. My MVP project was about a platformer, as well as about how I spent those entries notes to help me even further in this game I created. I also used these entries for notes. So that I can use them in the future. Like for the game I'm developing for this platformer project.   

## **Code Presentation:**

Since this is the code I have been working on for my platformer game for the last week, it will be harder to explain the code moving on since I'm still actively working on the platformer project. But before I get into the explanation of the game code I have to state again that code is still a work in progress so bare with the process for a bit.Like I said before, since this game preview is still a work in progress bare with the process so far because it will get better over the times I update the code in my MVP project: The Unname Platformer Game. Since that is the case I have been trying to simplify the whole process but that's about it so far. Anyways, enough context here is the full preview with the explanations;  

**Preview:**
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

Onto the whole process; The first part of the code that is read is having a set score that's has a `0` by `const score = add([ text("Score:"), pos(24, 24), { value: 0 }, ])` This empty `value` set to `0` can be change by the coins that are around the map as the player runs. Using the code at the very end of the script: `player.onCollide("coin", (enemy) => { score.value += 1; score.text = "Score:" + score.value })` will simply destroy the coin model. Which will also add a `value` of `+1` with the help of the string: `score.value += 1`. Which is saved into the text `value`, `score.text = "Score:" + score.value`. Secondly, in the script, the player needs a way to move ofcorose in almost every game needs the player to move around. With the help of this code: `onKeyDown("KEY_CODE", () => { player.move(-SPEED, 0) })` it just changes the `SPEED` index of the player with that all. Last and finally is the third part of the script is the end-game system of this. By allowing the model: `fire` be allowed to kill the player. By using this to do so; `player.onCollide("fire", (enemy) => { destroy(player) const die = add([ text("[ GAME OVER ]"), pos(500,200), ])` This, like I said destroys the player model and displays the "GAME OVER" screen onto the playable window. This is the make sure that game has a goal and an end. Like all games, it creates balence to the whole system. To make sure it's fun for the user to actally play the game for a bti of time.                  

### **Design Process:** 

For this design process, I have been thinking a lot about the fact that I feel my entries need to be less complicated in the sense that I have been over-explaining the topic to the point that it takes me at least two paragraphs to explain the topic! That is why I have been trying to make my entries less complicated like I said before; This is because of the simple fact that I have already shown how simple the code itself is by this. Due to this fact, it is easy to point out that parts of the code are in fact really complex at first looking at it. But after all that, it becomes super simple once it's all broken down. That was the main point I was trying to convien with this entry. 

### **Challenges & Takeaways:**

* I don't have many challenges but, one big challenge I had in my experience in this entry was that I didn't ask for help enough. This was mostly because I didn't use slack or any other help like before in the older entries. That's why it took so much planning and tinkering to make the code work out at the end. 

* Also, I had challenges with the fact that my I.D.E keeps lagging throughout the whole time I was testing out things for the project. This was not really no-ones fault but mine. Since I keeped overloading the IDE preview with all my broken up code. 

* Another challenge I faced was that I feel that I didn't tinker enough while working on this entry. Thus having a bit of a harder time working on this entry. Also, this could have been because of the fact that I keep getting errors, lagging, and plan bugs.
   
* One takeaway I had from this was that I need to tinker more in my own time while doing this entry.

* Another takeaway I got from this was that I felt that I rushed to get the project done as fast as possible. Which isn't great for the project itself. _(It would make it looked rushed)_
 
* I guess my last takeaway that I got from entry was that I felt like really bad that I didn't spend my time well in that I didn't make any time so that when I would do it was harder for me. Since I only spent three days working on the project. I thought it would take a lot shorter time to do the whole project. 
  







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










