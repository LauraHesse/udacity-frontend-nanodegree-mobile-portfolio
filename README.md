## Website Performance Optimization portfolio project

Your challenge, if you wish to accept it (and we sure hope you will), is to optimize this online portfolio for speed! In particular, optimize the critical rendering path and make this page render as quickly as possible by applying the techniques you've picked up in the [Critical Rendering Path course](https://www.udacity.com/course/ud884).

To get started, check out the repository and inspect the code.

### Getting started

#### Part 1: Optimize PageSpeed Insights score for index.html

Some useful tips to help you get started:

1. Check out the repository
1. To inspect the site on your phone, you can run a local server

  ```bash
  $> cd /path/to/your-project-folder
  $> python -m SimpleHTTPServer 8080
  ```

1. Open a browser and visit localhost:8080
1. Download and install [ngrok](https://ngrok.com/) to the top-level of your project directory to make your local server accessible remotely.

  ``` bash
  $> cd /path/to/your-project-folder
  $> ./ngrok http 8080
  ```

1. Copy the public URL ngrok gives you and try running it through PageSpeed Insights! Optional: [More on integrating ngrok, Grunt and PageSpeed.](http://www.jamescryer.com/2014/06/12/grunt-pagespeed-and-ngrok-locally-testing/)

Profile, optimize, measure... and then lather, rinse, and repeat. Good luck!

#### Part 2: Optimize Frames per Second in pizza.html

To optimize views/pizza.html, you will need to modify views/js/main.js until your frames per second rate is 60 fps or higher. You will find instructive comments in main.js.

You might find the FPS Counter/HUD Display useful in Chrome developer tools described here: [Chrome Dev Tools tips-and-tricks](https://developer.chrome.com/devtools/docs/tips-and-tricks).

### Optimization Tips and Tricks
* [Optimizing Performance](https://developers.google.com/web/fundamentals/performance/ "web performance")
* [Analyzing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/analyzing-crp.html "analyzing crp")
* [Optimizing the Critical Rendering Path](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/optimizing-critical-rendering-path.html "optimize the crp!")
* [Avoiding Rendering Blocking CSS](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/render-blocking-css.html "render blocking css")
* [Optimizing JavaScript](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/adding-interactivity-with-javascript.html "javascript")
* [Measuring with Navigation Timing](https://developers.google.com/web/fundamentals/performance/critical-rendering-path/measure-crp.html "nav timing api"). We didn't cover the Navigation Timing API in the first two lessons but it's an incredibly useful tool for automated page profiling. I highly recommend reading.
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/eliminate-downloads.html">The fewer the downloads, the better</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/optimize-encoding-and-transfer.html">Reduce the size of text</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/image-optimization.html">Optimize images</a>
* <a href="https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching.html">HTTP caching</a>

### Customization with Bootstrap
The portfolio was built on Twitter's <a href="http://getbootstrap.com/">Bootstrap</a> framework. All custom styles are in `dist/css/portfolio.css` in the portfolio repo.

* <a href="http://getbootstrap.com/css/">Bootstrap's CSS Classes</a>
* <a href="http://getbootstrap.com/components/">Bootstrap's Components</a>

#Website optomization
---
Files compressed: To Index.html, project-2048.html, project-mobile.html, project-webperf.html:

1. Added a @font-face request for Google fonts
1. Inlined CSS to prevent bocking page load.
1. Added media=print for "print" media query
1. JS has async and defer tag to avoid blocking page load.
1. Image links were replaced by dowloaded and compressed images.
1. In HTML, removed all the comment and unnecessary space to reduce file size.

File compressed: pizza.html

1. JS has async and defer tag to avoid blocking page load.
1. Inlined (crazy) compressed Bootstrap classes to prevent page blocking
1. In HTML, removed all the comment and unnecessary space to reduce file size.
1. Images were all replaced to compressed ones  

##File compressed: main.js
---
1. changePizzaSizes function - for loop was optimized to meet specifications for the pizza resize.

```javascript
// Iterates through pizza elements on the page and changes their widths
function changePizzaSizes(size) {
    var i;
    var container = document.getElementsByClassName('randomPizzaContainer');
    var dx = determineDx(container[0], size);
    var newwidth = (container[0].offsetWidth + dx) + 'px';
  for (i = 0; i < container.length; i++) {
    container[i].style.width = newwidth;
  }
}
```
---
2. Removed the calculations from the for loop.

```javascript
// Moves the sliding background pizzas based on scroll position
//
var items; //creating global variables intead

function updatePositions() {
  frame++;
  window.performance.mark("mark_start_frame");

  //creating local variables for easy access
  var itemsLength = items.length;
  var phase;
  var top = (document.body.scrollTop / 1250);

  //var items = document.querySelectorAll('.mover');
  //this main loop sets style ".style.left" for each of the pizzas - check .style.transform to offset position
  // items[i].style.left = items[i].basicLeft + 100 * phase[i % 5] + 'px';
  //reference - https://discussions.udacity.com/t/pizza-sliding-issue/202664

  var phaseArray = []; //creating an empty Array and generating it outside the for-loop
  for (var i = 0; i < 5; i++) {
    phaseArray.push(Math.sin(top + i));
  }

  // Reposition the pizzas
  for (var l = 0; l < itemsLength; l++){
    phase = phaseArray[l % 5];
    //https://discussions.udacity.com/t/project-4-how-do-i-optimize-the-background-pizzas-for-loop/36302/17
    //updatePositions main loop is setting the style.left of each of those pizza elements. This is a layout invalidator. We can look into using transform to offset position instead in combo with what is called a "z layer hack"
    items[l].style.transform = 'translateX(' + (100 * phase) + 'px)';
  }
```
---
3. Dynamically calculating how many scrolling pizzas are needed for the user's screen.

```javascript
// Reposition the pizzas
  for (var l = 0; l < itemsLength; l++){
    phase = phaseArray[l % 5];
    //https://discussions.udacity.com/t/project-4-how-do-i-optimize-the-background-pizzas-for-loop/36302/17
    //updatePositions main loop is setting the style.left of each of those pizza elements. This is a layout invalidator. We can look into using transform to offset position instead in combo with what is called a "z layer hack"
    items[l].style.transform = 'translateX(' + (100 * phase) + 'px)';
  }

  // User Timing API to the rescue again. Seriously, it's worth learning.
  // Super easy to create custom metrics.
  window.performance.mark("mark_end_frame");
  window.performance.measure("measure_frame_duration", "mark_start_frame", "mark_end_frame");
  if (frame % 10 === 0) {
    var timesToUpdatePosition = window.performance.getEntriesByName("measure_frame_duration");
    logAverageFrame(timesToUpdatePosition);
  }
}

// runs updatePositions on scroll
window.addEventListener('scroll', updatePositions);

// Generates the sliding pizzas when the page loads.
document.addEventListener('DOMContentLoaded', function() {
  var cols = 8;
  var s = 256;
  var elem; // variable outside the loop
  var allPizzas = 32;

  var movingPizzas = document.getElementById("movingPizzas1"); //get id out of the loop,prevent from multiple calling

  // recalculate cols and rows based on device browser window
  // https://github.com/uncleoptimus/udacityP4/blob/gh-pages/views/js/main.js
	cols = Math.ceil(window.innerWidth / (s - 73.33)); // factoring in img width for better effect
	rows = Math.ceil(window.innerHeight / s);
	allPizzas = cols * rows;
```
