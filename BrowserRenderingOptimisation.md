# Browser Rendering Optimisation
_22-Jul-16_

## The Critical Rendering Path
* Judders means not smooth.
* Most devices today refresh 60 frames per second i.e. 60 Hz / 60 fps.
* Render tree is DOM + CSSOM.
* Only elements to be rendered will make it to the render tree.
* After the render tree is built, it will calculate the space elements take up, turn them into boxes to form the layout. This step is  **Layout** in DevTools.
  * Layout can also be called reflow.
* Next step is **Vector to Raster** i.e. fill up the pixels. This step is **Paint** DevTools.
* Browser decodes **Encoded JPEG**. This is **Image Decode + Resize** in DevTools.
* Browsers might make **layers / composite layers** too. This is **Composite Layers** in DevTools. This step is done in the CPU, and then move to the GPU which instructs the construction of layers.
* the **Layout** process is complicated, it's best to assume that the scope is the entire document.
* Typical frame: JavaScript -> Style -> Layout -> Paint -> Composite. Depending on the change, some of these steps (Layout or Paint) for example, might not be executed.
  * Style - Browser recalculates the styles affected by the JavaScript.
* Resizing browser window for a loaded page will not result in recalculation of **Style**, but the browser will have to redo **Layout, Paint, and Composite**.
* http://www.csstriggers.com shows the CSS that could trigger **Layout, Paint, or Composite**.
* Not all CSS are created equal!

## App Lifecycles
* Should you always make your app run under 60 frames per second? No.
* Four major areas of an app lifecycle: RAIL - Response, Animations, Idle, and Load. But the correct chronological order is LIAR - Load, Idle, Animations, Response.
* **Load** - to be achieved in **1 second**.
* **Idle time** (typically broken down to **50ms** chunks)- At post-load idle state, it is good practice to load stuff that user tends to interact with later such as image assets, videos and comments section.
* **Response** - study shows that a limit of **100ms** after someone presses something on screen before they notice any lag.
*  **Animations** - limit is 60 fps i.e. **16ms**. In fact it's less than that because the browsers have overheads, hence we are typically left with 10-12ms.
* Transition animations (e.g. menu sliding in) needs to be 60 fps as well.
* Example trick for smooth animations: **FLIP**. This took advantage of the fact that once the browser done the initial hard work to run the animation once, running it backwards can be done at little cost.
  * First - where the element starts
  * Last - where the element finishes.
  * Invert - invert animations.
  * Play - play animations.
* Property collections can be expensive. Make sure it is done within the response window.
* Opacity and transform only affect the composite layer. Keep this in mind when building performant apps.
* Anything that involves movement or finger on screen interactions will need to run at 60 fps.

![time-table](http://udacity.github.io/60fps/images/time-table.jpg)

## Weapons of Jank Destruction
* [Link to How to use timeline DevTools](https://developers.google.com/web/tools/chrome-devtools/profile/evaluate-performance/timeline-tool?hl=en)
* Open a Chrome window in incognito mode to ensure environment with no extensions.

## JavaScript
* JavaScript compilers on a browsers run on Just In Time(JIT). This basically means we won't know exactly what code will run in the engine.
* Hence, avoid mirco-optimisation. For example, there is not much point to optimize by choosing between for loop and while loop since they are really similar.
* Optimizing JavaScript for Animations - make sure to run all JS within **10ms** (due to browser overheads). Good practice is to execute JS as early as possible each frame.
* `requestAnimationFrame` schedules JavaScript to run at the earliest possible moment. This can recalculation of styles.
* `setTimeout` and `setInterval` are frequently used back in the old days for animations. In fact, JQuery still uses them. The problem with these functions is that JavaScript engine pays no attention to the rendering pipeline when scheduling these. Not a good fit for animations.
* IE 9 unfortunately doesn't support `requestAnimationFrame`, hence use [polyfill](https://gist.github.com/paulirish/1579671) instead to avoid issues with compatibility.
* Reminder - run everything within 10-12ms.
* Don't leave JS Profiler on all the time, only use it when there is long running JS.
![The Pixel Pipeline](https://developers.google.com/web/fundamentals/performance/rendering/images/intro/frame-full.jpg)
* Careful with creating functions inside a loop (See Web Workers Solution), this can cause too much overhead for the browser.

#### Web Workers
* Web workers provide interface for spawning scripts to run in the background i.e. running JS in a totally different scope than the main window and on a totally separate operating system thread.
* **Web Worker** thread and **Main** thread can communicate with each other.
* [Web Workers Documentation](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API/Using_web_workers)
* Typical steps:
    * Declaring worker variable that points to the worker script and use `.postMessage` to send data to the worker script in main.js:
    ```JavaScript
    var worker = new Worker('path/worker.js');

    // In a function which data to be sent to worker script
    worker.postMessage({
      'data' : data,
      'dataTwo' : dataTwo
    });
    ```
    * In worker.js, import relevant scripts and use `.onmessage` function to collect data sent from main.js via `postmessage`.
    ```JavaScript
    this.onmessage = function(e) {
      var data = e.data.someData
      try {
        // some functions to be run if without errors
        postMessage(dataToBePosted);
      }
      catch (e) {
        // handles the error
        function ManipulationException(message) {
          this.name = 'ManipulationException';
          this.message = message;
        }
      }
      throw new ManipulationException('Error Message');
    }
    ```
    * Back in main.js, receive message via `.onmessage` and use `.onerror` to handle error.
    ```JavaScript
    worker.onmessage = function(e) {
      var dataReceived = e.data;
      // some code for further execution
    }

    worker.onerror = function(error) {
      // handles the error from worker.js
      function WorkerException(message) {
        this.name = 'WorkerException';
        this.message = message;
      }
      throw new WorkerException('Worker error');
    }

    ```


#### JavaScript Memory Management
* Use Chrome DevTools (Memory) to examine memory usage.
* If it shows a lot of fast climbs, that indicates we're assigning a lot of memory quickly and very often.
* When the garbage collector runs (free memory), does it take it back to zero. If not, there might be a memory leak.
* Use Cmd-f and search for **GC** to determine how long to it takes to collect garbage.
* Useful links:
  * [Writing Fast, Memory-Efficient JavaScript](https://www.smashingmagazine.com/2012/11/writing-fast-memory-efficient-javascript/)
  * [Memory Management on MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Memory_Management)
  * [High-Performance, Garbage-Collector-Friendly Game](http://buildnewgames.com/garbage-collector-friendly-code/)

## Styles and Layout
_23-Jul-16_
* Performance cost of **Recalculate Styles** scale with the number of elements affected by a style change **linearly**.
* Keep selector matching simple.
* It is more efficient to use class as selector rather than say the nth child of and element as selector.
* **B**lock **E**lement **M** odifier is one of the methods to write css class. [Link1](https://www.sitepoint.com/bem-smacss-advice-from-developers/) and [Link2](https://en.bem.info/methodology/key-concepts/) for more details.
```CSS
.block {}
.block__element {}
.block--modifier {}
.block__element--modifier {}
/*example*/
.menu {}
.menu__item{}
.menu__item--featured{}
.menu--footer{}
```

* Layout Thrashing - according to the pipeline, the browser does JS -> Style -> Layout -> Paint -> Composite. If a Layout change is initiated, then followed by a Style change, this would mean the browser would have to redo the layout again because it not valid. This is an expensive mistake to make.
  * When this happens, Dev tool will give a red flag and warning message saying '**Forced synchronous layout** is possible performance bottleneck'.
* Check [csstriggers.com](https://csstriggers.com) for CSS that triggers **Layout**.
* Scrolling e.g. `scrollY` or reading layouts (getting element height) `.offsetHeight` will cause the browser to run **Layout**. Hence we should avoid running style JS after these functions to avoid **forced synchronous layout**.
* Read [Scrolling Performance](http://www.html5rocks.com/en/tutorials/speed/scrolling/) by Paul Lewis for more info.
* Changing style first and read layout properties later in **loops** won't help because each time you change style you run layout, if it happens end of frame the browser will recalculate styles and run layout again, causing a FSL.
* Reading layout first in the JavaScript phase means you will be reading layout properties from the previous frame. Then **batch** style change such that recalculate happens only at the end of frame.
* Essentially any property for which a browser must run layout i.e. anything to do with geometrics and dimensions can cause FSL to happen.
* More information on FSL - [How (not) to trigger a layout in Webkit](http://gent.ilcore.com/2011/03/how-not-to-trigger-layout-in-webkit.html)
* Doing FSL many times is called layout thrashing.
* You can't trigger **Layout** without triggering **Paint**. Avoid triggering them during animation!

## Compositing and Painting
_26-Jul-16_
* Use **Show Paint Rectangles** in DevTools to see where **Painting** is happening.
* Use **Paint Profiler** to see the commands being called when painting.
* In Chrome DevTools, you might see different layers records. The first is **Update Layer Tree**, which happens when Chrome internal engine, called Blink, figures out what layers are needed for the page. **Composite Layers** is another record in which the browser put the page together and send to the screen.
* The more layers you have, the more time you spend compositing. Hence there is a tradeoff between reducing paint time and increasing layer management time.
* Use `will-change` on CSS to tell the browser that we intend to change the at some point, and to prepare for that the browser will create a new layer.
  * Older browser like safari uses `transform: translatez(0)`. This is called the **no transform hack**. This pushes the element to z-space to 0 but it's enough to convince the browser to create a new layer.
* Use paint profiler to do layer management.
