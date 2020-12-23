# Sensors

Sensors are an abstraction to detect different input methods in order to initiate drag operations, respond to movement and end or cancel the operation. 

Sensors may define one or multiple **activator events**. Activator events use React  [SyntheticEvent listeners](https://reactjs.org/docs/events.html), which leads to improved performance over manually adding event listeners to each individual draggable node.

Sensors are initialized once one of the activator events is detected.

The built-in sensors are:

* [Pointer](pointer.md)
* [Mouse](mouse.md)
* [Touch](touch.md)
* [Keyboard](keyboard.md)

If necessary, you may also implement custom sensors to respond to other inputs or if the built-in sensors do not suit your needs. If you build a custom sensor and you think others could benefit, don't hesitate to open an RFC pull request.

## Lifecycle

The lifecycle of a sensor is as follows:

* Activator event detected, if the event is qualified, sensor class is initialized. 
* Sensor manually attaches new listeners to input methods upon initialization.
* Sensor dispatches drag start event once constraints are met.
* Sensor dispatches drag move events in response to input.
* Sensor dispatches drag end or drag cancel event.
* Sensor is torn down and cleans up manually attached event listeners.

## Hooks

### useSensor



### useCombineSensors

