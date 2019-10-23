# Exporting your P5 sketch as a Scalable Vector Graphic file

There is a very useful (though unmaintained) [p5 library](http://github.com/zenozeng/p5.js-svg) that you can use to convert the drawing that you do on screen into a vector graphic that you can then edit using Illustrator, Sketch, or any other app thatunderstands the SVG format. This can save you from having to sweat all the compositional and typographic details within your code and allows you to focus on *just the diagram* knowing that you can then resize, crop, and label it later on ‘by hand’.

There are three things you need to do differently than in a ‘vanilla’ p5 sketch to make this work:

### 1. Add the `p5.svg` library to your HTML file

Your p5 projects always consist of a pair of files: the `.js` file where you define your `setup()` & `draw()` functions and the `.html` file that you open in the browser. The HTML file is responsible for loading your `.js` file and all the libraries that your code depends on (in particular the `p5.min.js` library). 

Look in the HTML for a block of `<script>` tags to see where everything else is being loaded and note that *your* `.js` file is listed **last**. This is essential since your code ‘depends’ on all those other files and they will be loaded in the order in which they’re listed.

To enable SVG export, make sure the following line is in the HTML file **right before** your code file is mentioned:

```html
<script src="../libraries/p5.svg.js"></script>
```

In a typical project HTML file it’d look something like this:

```html
  <!-- the main p5 library -->
  <script src="../libraries/p5.min.js"></script>

  <!-- the special SVG-export library  -->
  <script src="../libraries/p5.svg.js"></script>

  <!-- your code -->
  <script src="sketch.js"></script>
```

### 2. Create a canvas that renders to SVG rather than a bitmap

Typically, the first thing you do in your `setup()` function is choose a width and height for your sketch using the `createCanvas()` command. To enable SVG output, you need to add one more argument (defining the format) to your call after passing the width and height.

If your sketch currently sets the screen size like this:

```js
setup(){
	createCanvas(800, 600)
}
```

All you need to add is the variable name `SVG` at the end:

```js
setup(){
	createCanvas(800, 600, SVG)
}
```

### 3. At the end of your drawing routine, call `save()`

The `p5.svg` library adds a new command called `save()` which takes one argument: a filename. You should call it at the **end** of your drawing routine, once the graphic is complete. It will then cause the browser to download a file with the name you specified containing your graphics. Make sure the filename ends with `.svg` so other applications will know what the file contains. For instance try something like:

```js
save('my-sketch.svg')
```

## Caveats

The `p5.svg` library hasn’t been updated in quite a few years at this point and some of its seams are beginning to show. The primary issue is that it incorrectly sizes the image that gets displayed in the browser, only showing the upper-left quarter of the image. This doesn't affect its output though: the downloaded SVG is fine.

This does make it a little harder to preview your work on-screen then quickly save a file when you’re actively developing. A template that might help would be to define a boolean that switches modes:

```js
var shouldExport = true
var exportFilename = 'my-sketch.svg'

function setup(){
  var canvasW = 300
  var canvasH = 200

  if (shouldExport)
    createCanvas(canvasW, canvasH, SVG)
  }else{
    createCanvas(canvasW, canvasH)
  }

  //
  // 	... insert your drawing code here ...
  //
  
  if (shouldExport){
    save(exportFilename)
  }
}
```

With this setup you can just toggle the `shouldExport` variable between `true` and `false` to switch between ‘preview’ and ‘output’ modes while working.

### Do your drawing in `setup()` not `draw()`
