# **Entry Log #2**
### **Nayer Ebraheim - 12/9/24**

---

### **_Intro:_**

Doing this entry has taught me a ton about how _kaboom.js_ works in this code space. This can be because of the fact that I have been tinkering a lot in _kaboom.js_ for quite a while now. I have learned that like I said before; I started thinking with _kaboom.js_. overall, _kaboom.js_ I just started researching about my tool _kaboom.js_. And what I found out was that it's pretty simple for a baseline JS tool. You can see this here in the code presentation. In this entry log, I will also be talking about my challenges in this entry as well as my takeaways.


### **_Code Presentation:_**

Starting off, this part of _kaboom.js_ was mostly just me relearning things from my last entry log. This was because of the fact I forgot a ton of stuff from the last entry log. But to make-up for that lost time. I had restarted tinkering with _kaboom.js_ for a while now. To show what I have learned,

In this code presentation these are some things I have learned;

```JS
kaboom({
	background: [212, 110, 179],
})

// Load a custom font from a .ttf file
loadFont("FlowerSketches", "/examples/fonts/FlowerSketches.ttf")

// Load a custom font with options
loadFont("apl386", "/examples/fonts/apl386.ttf", { outline: 4, filter: "linear" })

// Load custom bitmap font, specifying the width and height of each character in the image
loadBitmapFont("unscii", "/examples/fonts/unscii_8x8.png", 8, 8)
loadBitmapFont("4x4", "/examples/fonts/4x4.png", 4, 4)

// List of built-in fonts ("o" at the end means the outlined version)
const builtinFonts = [
	"monospace",
]

// Make a list of fonts that we cycle through
const fonts = [
	...builtinFonts,
	"4x4",
	"unscii",
	"FlowerSketches",
	"apl386",
	"Sans-Serif",
]

// Keep track which is the current font
let curFont = 0
let curSize = 48
const pad = 24

// Add a game object with text() component + options
const input = add([
	pos(pad),
	// Render text with the text() component
	text("Type! And try arrow keys!", {
		// What font to use
		font: fonts[curFont],
		// It'll wrap to next line if the text width exceeds the width option specified here
		width: width() - pad * 2,
		// The height of character
		size: curSize,
		// Text alignment ("left", "center", "right", default "left")
		align: "left",
		lineSpacing: 8,
		letterSpacing: 4,
		// Transform each character for special effects
		transform: (idx, ch) => ({
			color: hsl2rgb((time() * 0.2 + idx * 0.1) % 1, 0.7, 0.8),
			pos: vec2(0, wave(-4, 4, time() * 4 + idx * 0.5)),
			scale: wave(1, 1.2, time() * 3 + idx),
			angle: wave(-9, 9, time() * 3 + idx),
		}),
	}),
])

// Like onKeyPressRepeat() but more suitable for text input.
onCharInput((ch) => {
	input.text += ch
})

// Like onKeyPress() but will retrigger when key is being held (which is similar to text input behavior)
// Insert new line when user presses enter
onKeyPressRepeat("enter", () => {
	input.text += "\n"
})

// Delete last character
onKeyPressRepeat("backspace", () => {
	input.text = input.text.substring(0, input.text.length - 1)
})

// Go to previous font
onKeyPress("left", () => {
	if (--curFont < 0) curFont = fonts.length - 1
	input.font = fonts[curFont]
})

// Go to next font
onKeyPress("right", () => {
	curFont = (curFont + 1) % fonts.length
	input.font = fonts[curFont]
})

const SIZE_SPEED = 32
const SIZE_MIN = 12
const SIZE_MAX = 120

// Increase text size
onKeyDown("up", () => {
	curSize = Math.min(curSize + dt() * SIZE_SPEED, SIZE_MAX)
	input.textSize = curSize
})

// Decrease text size
onKeyDown("down", () => {
	curSize = Math.max(curSize - dt() * SIZE_SPEED, SIZE_MIN)
	input.textSize = curSize
})

// Use this syntax and style option to style chunks of text, with CharTransformFunc.
add([
	text("[green]oh hi[/green] here's some [wavy]styled[/wavy] text", {
		width: width(),
		styles: {
			"green": {
				color: rgb(128, 128, 255),
			},
			"wavy": (idx, ch) => ({
				color: hsl2rgb((time() * 0.2 + idx * 0.1) % 1, 0.7, 0.8),
				pos: vec2(0, wave(-4, 4, time() * 6 + idx * 0.5)),
			}),
		},
	}),
	pos(pad, height() - pad),
	anchor("botleft"),
	// scale(0.5),
])


```

Here is a step by step list of how this code presentation works.

First:

To start of the code snippent, the first thing that happends are the custom  

```JS
// Load a custom font from a .ttf file
loadFont("FlowerSketches", "/examples/fonts/FlowerSketches.ttf")

// Load a custom font with options
loadFont("apl386", "/examples/fonts/apl386.ttf", { outline: 4, filter: "linear" })

// Load custom bitmap font, specifying the width and height of each character in the image
loadBitmapFont("unscii", "/examples/fonts/unscii_8x8.png", 8, 8)
loadBitmapFont("4x4", "/examples/fonts/4x4.png", 4, 4)

// List of built-in fonts ("o" at the end means the outlined version)
const builtinFonts = [
	"monospace",
]
```

Second:

```JS
// Make a list of fonts that we cycle through
const fonts = [
	...builtinFonts,
	"4x4",
	"unscii",
	"FlowerSketches",
	"apl386",
	"Sans-Serif",
]

// Keep track which is the current font
let curFont = 0
let curSize = 48
const pad = 24

// Add a game object with text() component + options
const input = add([
	pos(pad),
	// Render text with the text() component
	text("Type! And try arrow keys!", {
		// What font to use
		font: fonts[curFont],
		// It'll wrap to next line if the text width exceeds the width option specified here
		width: width() - pad * 2,
		// The height of character
		size: curSize,
		// Text alignment ("left", "center", "right", default "left")
		align: "left",
		lineSpacing: 8,
		letterSpacing: 4,
		// Transform each character for special effects
		transform: (idx, ch) => ({
			color: hsl2rgb((time() * 0.2 + idx * 0.1) % 1, 0.7, 0.8),
			pos: vec2(0, wave(-4, 4, time() * 4 + idx * 0.5)),
			scale: wave(1, 1.2, time() * 3 + idx),
			angle: wave(-9, 9, time() * 3 + idx),
		}),
	}),
])
```

Third:

```JS
// Like onKeyPressRepeat() but more suitable for text input.
onCharInput((ch) => {
	input.text += ch
})

// Like onKeyPress() but will retrigger when key is being held (which is similar to text input behavior)
// Insert new line when user presses enter
onKeyPressRepeat("enter", () => {
	input.text += "\n"
})

// Delete last character
onKeyPressRepeat("backspace", () => {
	input.text = input.text.substring(0, input.text.length - 1)
})

// Go to previous font
onKeyPress("left", () => {
	if (--curFont < 0) curFont = fonts.length - 1
	input.font = fonts[curFont]
})

// Go to next font
onKeyPress("right", () => {
	curFont = (curFont + 1) % fonts.length
	input.font = fonts[curFont]
})

const SIZE_SPEED = 32
const SIZE_MIN = 12
const SIZE_MAX = 120

// Increase text size
onKeyDown("up", () => {
	curSize = Math.min(curSize + dt() * SIZE_SPEED, SIZE_MAX)
	input.textSize = curSize
})

// Decrease text size
onKeyDown("down", () => {
	curSize = Math.max(curSize - dt() * SIZE_SPEED, SIZE_MIN)
	input.textSize = curSize
})
```

Fourth:

```JS

// Use this syntax and style option to style chunks of text, with CharTransformFunc.
add([
	text("[green]oh hi[/green] here's some [wavy]styled[/wavy] text", {
		width: width(),
		styles: {
			"green": {
				color: rgb(128, 128, 255),
			},
			"wavy": (idx, ch) => ({
				color: hsl2rgb((time() * 0.2 + idx * 0.1) % 1, 0.7, 0.8),
				pos: vec2(0, wave(-4, 4, time() * 6 + idx * 0.5)),
			}),
		},
	}),
	pos(pad, height() - pad),
	anchor("botleft"),
	// scale(0.5),
])


```

### **_Challenges / Takeaways:_**

* One big challenge I had in my experience in this entry was that I didn't ask for help enough.
	* _Mostly because I didn't use slack or any other help._

* One takeaway I have was that I need to tinker more with my tool.
	* _Can because I can get a better knowing of what my tool can do._


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





