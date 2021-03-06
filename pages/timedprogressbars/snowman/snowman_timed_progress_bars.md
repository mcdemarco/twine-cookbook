# "Timed Progress Bars": Snowman (v1.3.0)

## Summary

"Timed Progress Bars" creates a global *window.setup* object and function, **timedprogressbars()**. Using jQuery within the definition, the function creates outer and inner `<div>` elements with CSS classes defined in the Story Stylesheet. Using a combination of **[setInterval()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setInterval)** and **[setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout)**, a timer is created. The length and color of an inner `<div>` element is adjusted based on the remaining time each loop.

When the timer runs out, the function argument is run and the length of the inner `<div>` element is reduced to 0.

## Example

[Download](snowman_timed_progress_bars_example.html)

## Twee Code

```twee
:: StoryTitle
Timed Progress Bars in Snowman

:: UserScript[script]
// Use or create window.setup
window.setup = window.setup || {};

/*

    Description: Show a dynamically-created "progress bar"
    that changes colors as its timer runs down.

    Arguments:
      duration: time in seconds
      width: CSS width
      functionToRun: the function to execute when the timer runs out

*/

setup.timedprogressbars = function(duration, width, functionToRun) {
  
    // Save or generate a default duration
    var duration = (Number(duration) || 60) * 1000;

    // Save or generate a width
    var width = width || "100%";

    // Generate a unique hash
    var hash = Math.floor(Math.random() * 0x100000000).toString(16);

    //  Create an outer ID
    var outerId = "outer_" + hash;

    // Create an inner ID
    var innerId = "inner_" + hash;

    // Create an outer div,
    // add an ID,
    // add a class,
    // change the CSS width, and
    // append to the output
    var progressbar = $("<div>")
    .attr("id", outerId)
    .addClass("progress-bar")
    .css('width', width)
    .appendTo("#passage");

    // Create an inner div,
    // add an ID,
    // add a class,
    // change the CSS width, and
    // append to the progressbar
    var progressvalue = $("<div>")
    .attr("id", innerId)
    .addClass("progress-value")
    .css('width', "100%")
    .appendTo(progressbar);

    // Create a function to convert into hexadecimal
    var toHex = function(num) {
      var res = Math.round(Number(num)).toString(16);
      return (res.length === 1 ? "0" + res : res);
    };

    // Watch for the :passagedisplay event once
    jQuery(document).one("showpassage:after", function() {

      // Get the current time
      var timeStarted = (new Date()).getTime();

      // Save a reference to the setInterval function
      var workFunction = setInterval(function() {

        // Check if the element is still 'connected'
        if(! progressbar.prop("isConnected") ) {

          // Navigated away from the passage
          clearInterval(workFunction);
          return;
        }

        // Figure out how much time has passed
        var timePassed = (new Date()).getTime() - timeStarted;

        // Check if the timer has run out
        if(timePassed >= duration) {

          // Reduce the inner width to 0
          progressvalue.css('width', "0");

          // Clear interval
          clearInterval(workFunction);

          // Run the inner function (if set)
          setTimeout(functionToRun, 40);
          return;
        }

        // Update the progress percentage
        var percentage = 100 - 100 * timePassed / duration;

        // Save the new color
        var color = "#"
          + toHex(Math.min(255, 510 -  5.1 * percentage))
          + toHex(Math.min(255, 5.1 * percentage)) + "00";

        // Update the background color of the inner div
        progressvalue.css("backgroundColor", color);

        // Update the inner div width
        progressvalue.css("width", (100 - 100 * timePassed / duration) + "%");

      }, 40);

    });
};


:: UserStylesheet[stylesheet]
.progress-bar {
  position: relative;
  border: 1px solid #777;
  background: black;
  height: 1em;
}
.progress-value {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  background: #00ff00;
}

:: Start
<script>

  setup.timedprogressbars(5, "20em", function(){
    // Hide the progress bar
    $(".progress-bar").css("display", "none");
    // Display the result
    $("#results").text("Too late!");
  });

</script>

<div id="results"></div>


```

[Twee Download](snowman_timed_progress_bars_twee.txt)

## See Also

[Adding Functionality](../../addingfunctionality/snowman/snowman_adding_functionality.md)
