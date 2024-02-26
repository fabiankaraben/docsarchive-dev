---
linkTitle: "SyntheticEvent"
title: "SyntheticEvent – React"
description: ""
weight: 6
type: docs
---

# SyntheticEvent

This reference guide documents the `SyntheticEvent` wrapper that forms part of React’s Event System. See the [Handling Events](/react/18.1/main-concepts/handling-events) guide to learn more.

## Overview {#overview}

Your event handlers will be passed instances of `SyntheticEvent`, a cross-browser wrapper around the browser’s native event. It has the same interface as the browser’s native event, including `stopPropagation()` and `preventDefault()`, except the events work identically across all browsers. 

If you find that you need the underlying browser event for some reason, simply use the `nativeEvent` attribute to get it. The synthetic events are different from, and do not map directly to, the browser’s native events. For example in `onMouseLeave``event.nativeEvent` will point to a `mouseout` event. The specific mapping is not part of the public API and may change at any time. Every `SyntheticEvent` object has the following attributes:

```jsx
boolean bubbles
boolean cancelable
DOMEventTarget currentTarget
boolean defaultPrevented
number eventPhase
boolean isTrusted
DOMEvent nativeEvent
void preventDefault()
boolean isDefaultPrevented()
void stopPropagation()
boolean isPropagationStopped()
void persist()
DOMEventTarget target
number timeStamp
string type
```

> Note:
> As of v17, `e.persist()` doesn’t do anything because the `SyntheticEvent` is no longer [pooled ↗](https://reactjs.org/docs/legacy-event-pooling.html).
> 

> Note:
> As of v0.14, returning `false` from an event handler will no longer stop event propagation. Instead, `e.stopPropagation()` or `e.preventDefault()` should be triggered manually, as appropriate.
> 

## Supported Events {#supported-events}

React normalizes events so that they have consistent properties across different browsers.

The event handlers below are triggered by an event in the bubbling phase. To register an event handler for the capture phase, append `Capture` to the event name; for example, instead of using `onClick`, you would use `onClickCapture` to handle the click event in the capture phase.

- [Clipboard Events](/react/18.1/api-reference/events#clipboard-events)
- [Composition Events](/react/18.1/api-reference/events#composition-events)
- [Keyboard Events](/react/18.1/api-reference/events#keyboard-events)
- [Focus Events](/react/18.1/api-reference/events#focus-events)
- [Form Events](/react/18.1/api-reference/events#form-events)
- [Generic Events](/react/18.1/api-reference/events#generic-events)
- [Mouse Events](/react/18.1/api-reference/events#mouse-events)
- [Pointer Events](/react/18.1/api-reference/events#pointer-events)
- [Selection Events](/react/18.1/api-reference/events#selection-events)
- [Touch Events](/react/18.1/api-reference/events#touch-events)
- [UI Events](/react/18.1/api-reference/events#ui-events)
- [Wheel Events](/react/18.1/api-reference/events#wheel-events)
- [Media Events](/react/18.1/api-reference/events#media-events)
- [Image Events](/react/18.1/api-reference/events#image-events)
- [Animation Events](/react/18.1/api-reference/events#animation-events)
- [Transition Events](/react/18.1/api-reference/events#transition-events)
- [Other Events](/react/18.1/api-reference/events#other-events)

---

## Reference {#reference}

### Clipboard Events {#clipboard-events}

Event names:

```text
onCopy onCut onPaste
```

Properties:

```jsx
DOMDataTransfer clipboardData
```

---

### Composition Events {#composition-events}

Event names:

```text
onCompositionEnd onCompositionStart onCompositionUpdate
```

Properties:

```jsx
string data
```

---

### Keyboard Events {#keyboard-events}

Event names:

```text
onKeyDown onKeyPress onKeyUp
```

Properties:

```jsx
boolean altKey
number charCode
boolean ctrlKey
boolean getModifierState(key)
string key
number keyCode
string locale
number location
boolean metaKey
boolean repeat
boolean shiftKey
number which
```

The `key` property can take any of the values documented in the [DOM Level 3 Events spec ↗](https://www.w3.org/TR/uievents-key/#named-key-attribute-values).

---

### Focus Events {#focus-events}

Event names:

```text
onFocus onBlur
```

These focus events work on all elements in the React DOM, not just form elements.

Properties:

```jsx
DOMEventTarget relatedTarget
```

#### onFocus {#onfocus}

The `onFocus` event is called when the element (or some element inside of it) receives focus. For example, it’s called when the user clicks on a text input.

```jsx
function Example() {
  return (
    <input
      onFocus={(e) => {
        console.log('Focused on input');
      }}
      placeholder="onFocus is triggered when you click this input."
    />
  )
}
```

#### onBlur {#onblur}

The `onBlur` event handler is called when focus has left the element (or left some element inside of it). For example, it’s called when the user clicks outside of a focused text input.

```jsx
function Example() {
  return (
    <input
      onBlur={(e) => {
        console.log('Triggered because this input lost focus');
      }}
      placeholder="onBlur is triggered when you click this input and then you click outside of it."
    />
  )
}
```

#### Detecting Focus Entering and Leaving {#detecting-focus-entering-and-leaving}

You can use the `currentTarget` and `relatedTarget` to differentiate if the focusing or blurring events originated from *outside* of the parent element. Here is a demo you can copy and paste that shows how to detect focusing a child, focusing the element itself, and focus entering or leaving the whole subtree.

```jsx
function Example() {
  return (
    <div
      tabIndex={1}
      onFocus={(e) => {
        if (e.currentTarget === e.target) {
          console.log('focused self');
        } else {
          console.log('focused child', e.target);
        }
        if (!e.currentTarget.contains(e.relatedTarget)) {
          // Not triggered when swapping focus between children
          console.log('focus entered self');
        }
      }}
      onBlur={(e) => {
        if (e.currentTarget === e.target) {
          console.log('unfocused self');
        } else {
          console.log('unfocused child', e.target);
        }
        if (!e.currentTarget.contains(e.relatedTarget)) {
          // Not triggered when swapping focus between children
          console.log('focus left self');
        }
      }}
    >
      <input id="1" />
      <input id="2" />
    </div>
  );
}
```

---

### Form Events {#form-events}

Event names:

```text
onChange onInput onInvalid onReset onSubmit 
```

For more information about the onChange event, see [Forms](/react/18.1/main-concepts/forms).

---

### Generic Events {#generic-events}

Event names:

```text
onError onLoad
```

---

### Mouse Events {#mouse-events}

Event names:

```text
onClick onContextMenu onDoubleClick onDrag onDragEnd onDragEnter onDragExit
onDragLeave onDragOver onDragStart onDrop onMouseDown onMouseEnter onMouseLeave
onMouseMove onMouseOut onMouseOver onMouseUp
```

The `onMouseEnter` and `onMouseLeave` events propagate from the element being left to the one being entered instead of ordinary bubbling and do not have a capture phase.

Properties:

```jsx
boolean altKey
number button
number buttons
number clientX
number clientY
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
number pageX
number pageY
DOMEventTarget relatedTarget
number screenX
number screenY
boolean shiftKey
```

---

### Pointer Events {#pointer-events}

Event names:

```text
onPointerDown onPointerMove onPointerUp onPointerCancel onGotPointerCapture
onLostPointerCapture onPointerEnter onPointerLeave onPointerOver onPointerOut
```

The `onPointerEnter` and `onPointerLeave` events propagate from the element being left to the one being entered instead of ordinary bubbling and do not have a capture phase.

Properties:

As defined in the [W3 spec ↗](https://www.w3.org/TR/pointerevents/), pointer events extend [Mouse Events](/react/18.1/api-reference/events#mouse-events) with the following properties:

```jsx
number pointerId
number width
number height
number pressure
number tangentialPressure
number tiltX
number tiltY
number twist
string pointerType
boolean isPrimary
```

A note on cross-browser support:

Pointer events are not yet supported in every browser (at the time of writing this article, supported browsers include: Chrome, Firefox, Edge, and Internet Explorer). React deliberately does not polyfill support for other browsers because a standard-conform polyfill would significantly increase the bundle size of `react-dom`.

If your application requires pointer events, we recommend adding a third party pointer event polyfill.

---

### Selection Events {#selection-events}

Event names:

```text
onSelect
```

---

### Touch Events {#touch-events}

Event names:

```text
onTouchCancel onTouchEnd onTouchMove onTouchStart
```

Properties:

```jsx
boolean altKey
DOMTouchList changedTouches
boolean ctrlKey
boolean getModifierState(key)
boolean metaKey
boolean shiftKey
DOMTouchList targetTouches
DOMTouchList touches
```

---

### UI Events {#ui-events}

Event names:

```text
onScroll
```

> Note
> Starting with React 17, the `onScroll` event **does not bubble** in React. This matches the browser behavior and prevents the confusion when a nested scrollable element fires events on a distant parent.
> 

Properties:

```jsx
number detail
DOMAbstractView view
```

---

### Wheel Events {#wheel-events}

Event names:

```text
onWheel
```

Properties:

```jsx
number deltaMode
number deltaX
number deltaY
number deltaZ
```

---

### Media Events {#media-events}

Event names:

```text
onAbort onCanPlay onCanPlayThrough onDurationChange onEmptied onEncrypted
onEnded onError onLoadedData onLoadedMetadata onLoadStart onPause onPlay
onPlaying onProgress onRateChange onSeeked onSeeking onStalled onSuspend
onTimeUpdate onVolumeChange onWaiting
```

---

### Image Events {#image-events}

Event names:

```text
onLoad onError
```

---

### Animation Events {#animation-events}

Event names:

```text
onAnimationStart onAnimationEnd onAnimationIteration
```

Properties:

```jsx
string animationName
string pseudoElement
float elapsedTime
```

---

### Transition Events {#transition-events}

Event names:

```text
onTransitionEnd
```

Properties:

```jsx
string propertyName
string pseudoElement
float elapsedTime
```

---

### Other Events {#other-events}

Event names:

```text
onToggle
```
