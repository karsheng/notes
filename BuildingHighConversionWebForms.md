# Building High Conversion Web Forms
## Efficient input

1. Good form:
    1. Quick to finish
    1. Placeholders
    1. Fonts are visible and big enough
    1. Progress bar
1. Example of super efficient form: Amazon One-click Ordering
1. Make autocomplete and autofill possible
1. Death to Dropdowns - there must be a better input
1. Use correct HTML5 input types: tel, url, email (important for mobile) [MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-type)
1. `<datalist>` - works like dropdown but also allows you to type as well. [MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/datalist)
1. Perfect form is no form. Nobody wants to fill in forms.
1. Speed equals conversion.
1. Nest labels - use `<label>` element as a container for your inputs. Associated input get nested inside labels. This also improves the touch target size. Easier to get the form input focus.
  ```HTML
  <label for="myForm">
    <span>Form Label</span>
    <input type="button" id="myForm" value="">
  </label>
  ```
1. Label is next to input element in landscape, above when portrait.
1. Use placeholders.
1. Calendar and [styling it](https://www.tjvantoll.com/2013/04/15/list-of-pseudo-elements-to-style-form-controls/#input_date) and [Calendar Demo](http://codepen.io/greenido/pen/xwGEWO)
1. Use autocomplete! Link to full list of [autocomplete attributes](https://developers.google.com/web/updates/2015/06/checkout-faster-with-autofill?hl=en).
  ```HTML
  <!-- Autocomplete email -->
  <input type="email" name="myEmail" value="" placeholder="Your Email Address" required autocomplete="email">
  ```
1. Autofocus automatically puts the cursor or input making it easy for users to quickly begin using the form. Mobile browsers ignore autofocus such that the keyboard doesn't automatically appear.
1. It is recommended to use autofocus above the fold otherwise it will scroll down immediately upon rendering.
1. Reusing info will make forms faster - e.g. shipping addresses.
1. Validate users input - use both front end validation (for good user experience) and server side validation (important!). Website is not safe if you don't validate your backend. Use HTML5 attributes to do that.
1. Use `required` attribute to let users know that the input must be filled out in order to finish the form.
1. E.g. numeric inputs, range inputs
1. Can't prevent people from typing other non numeric values in a numeric input form, but the buttons for increment and decrement can be designed for good user experience.
1. Can use [pattern](http://www.w3schools.com/tags/att_input_pattern.asp) to validate stuff too. Like forcing input to be "A, B, C, D or E".
1. Can even, using JavaScript, [`setCustomValidity()`](https://developer.mozilla.org/en-US/docs/Web/API/HTMLSelectElement/setCustomValidity).
  ```JavaScript
  <script>
    var puppyInput = document.querySelector('#puppy-input');
    var submit = document.querySelector('#submit');

    submit.onclick = function () {
      if (puppyInput.value !== "puppy") {
        puppyInput.setCustomValidity("The input should say 'puppy'. You typed: '" + puppyInput.value + "'");
      } else {
        puppyInput.setCustomValidity("");
      }
    };
  ```

### Form Principles
1. Make forms short and sweet
1. Provide helpful prompts
1. Provide immediate feedback
1. [Web forms the right way](http://www.slideshare.net/greenido/web-forms-the-right-way)
1. Outside in view - have someone role play the role of a form
  1. [LukeW Interview](https://www.youtube.com/watch?v=taQfmw9wAVc)
1. Minimize gated shopping experience which pushes users away
1. E-commerce: Avoid registration gates at checkout - allow people to buy as gates
1. To allow users to continue access a page at different devices:
  1. Share an item through social channels or email
  1. Save to a shopping cart
1. How to design a form: Keep, Cut, Postpone, or Kill
  1. KEEP - Keep info that is critical, required, we've got to have it in the form somewhere
  1. CUT - We don't need it, so don't include it.
  1. POSTPONE - Things that you can ask later. A lot of info that was forced to give upfront which isn't applicable and actually has a better time and place further down in the flow. Postponing questions until they are appropriate.
  1. EXPLAIN - Telling people why you are asking for something.
1. What's the right input control for this type of question?
1. How do I minimize typing mistake?
1. How do I bound people so that they don't go into an error state?
1. Use[ Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation/Using_geolocation) to autocomplete addresses for users.
1. Use Google's [Polymer](https://www.polymer-project.org/1.0/)
1. Mobile first - mobile's a huge opportunity, mobile has new capabilities e.g. we can know down to 50m radius in terms of your location, multitouch etc.
1. Mobile forces you to prioritize, down to it's core essence.

### Touch Support
1. The fastest way to support touch is to change the UI in response to a DOM elements change in state. DOM elements can be in one of the following states: :default, :hover, :focus, :active.
1. To change UI for each of these states, we apply styles to the following pseudo classes :active, :focus, and :hover.
1. On most mobile browsers, :hover and :focus states will apply to an element after it's being tapped. Consider carefully what size you set and how they will look to the user after they finish their touch.
1. Bear in mind that anchor tags and buttons may have different behaviors in different browsers.
1. Safari on iOS does not apply the active state by default, to get it working you need to add a `touchstart` event listener to the `document body` or to each element. [Reference](https://developers.google.com/web/fundamentals/design-and-ui/input/touch/active-states?hl=en#enabling-active-state-support-on-ios)
1. Some browsers allow user to select text when long pressing on the screen. To prevent this use the user-select CSS property:
```
-moz-user-select: none;
-webkit-user-select: none;
-ms-user-select: none;
user-select: none;
```
1. iOS doesn't have `:focus`. Android devices have all three. Different devices behave differently.
1. One thumb eyeball - How to get things done with actions limited to a thumb and eyeball movement.
1. Various touch events:
Use the event object to tailor actions to events. Each touch event includes three lists of touches.
`touches`: a list of all fingers currently on the screen.
`targetTouches`: a list of fingers on the current DOM element.
`changedTouches`: a list of fingers involved in the current event. For example, in a touchend event, this will be the finger that was removed.
These lists consist of objects that contain touch information:

`identifier`: a number that uniquely identifies the current finger in the touch session.
`target`: the DOM element that was the target of the action.
`client/page/screen` coordinates: where on the screen the action happened.
`radius` coordinates and rotationAngle: describe the ellipse that approximates finger shape.
1. Udacity slider quiz / example `dropbox/udacity/senior web developer/slider/`
1. [BUILDING HIGH CONVERSION WEB FORMS: CHECKLIST](http://labs.udacity.com/images/web-forms-checklist.pdf?_ga=1.240360067.1239137622.1465368115)
