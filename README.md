# Prompt - from https://play.elevatorsaga.com/documentation.html

Your task is to program the movement of elevators, by writing a program in JavaScript.

The goal is to transport people in an efficient manner.
Depending on how well you do it, you can progress through the ever more difficult challenges.
Only the very best programs will be able to complete all the challenges.

Your code must declare an object containing at least two functions called init and update. Like this:

{
    init: function(elevators, floors) {
        // Do stuff with the elevators and floors, which are both arrays of objects
    },
    update: function(dt, elevators, floors) {
        // Do more stuff with the elevators and floors
        // dt is the number of game seconds that passed since the last time update was called
    }
}

These functions will then be called by the game during the challenge.
init will be called when the challenge starts, and update repeatedly during the challenge.

Normally you will put most of your code in the init function, to set up event listeners and logic.

Code examples

How to control an elevator

elevator.goToFloor(1);

Tell the elevator to move to floor 1 after completing other tasks, if any. Note that this will have no effect if the elevator is already queued to go to that floor.

if(elevator.currentFloor() > 2) { ... }

Calling currentFloor gets the floor number that the elevator currently is on. Note that this is a rounded number and does not necessarily mean the elevator is in a stopped state.

Listening for events

It is possible to listen for events, like when stopping at a floor, or a button has been pressed.

elevator.on("idle", function() { elevator.goToFloor(0); });

Listen for the "idle" event issued by the elevator, when the task queue has been emptied and the elevator is doing nothing. In this example we tell it to move to floor 0.

elevator.on("floor_button_pressed", function(floorNum) { ... } );

Listen for the "floor_button_pressed" event, issued when a passenger pressed a button inside the elevator. This indicates that the passenger wants to go to that floor.

floor.on("up_button_pressed", function() { ... } );

Listen for the "up_button_pressed" event, issued when a passenger pressed the up button on the floor they are waiting on. This indicates that the passenger wants to go to another floor.


# Response

To program the movement of the elevators in this challenge, you would need to use the information provided in the description to create an object with the required init and update functions.

The init function should be used to set up any event listeners and initial logic for the elevators and floors. For example, you could set up an event listener for the "idle" event for each elevator, and in the callback for that event, specify the logic for what the elevator should do when it is idle (such as moving to a certain floor or waiting for a passenger to press a button).

The update function should be used to implement any logic that needs to be repeated on a regular basis, such as checking for passengers waiting on floors and directing elevators to those floors. The dt parameter passed to this function specifies the number of game seconds that have passed since the last time the update function was called, so you can use this to control the frequency at which the function is executed.

Here is an example of how the object with the init and update functions might look:

```
{
    init: function(elevators, floors) {
        // Set up event listeners for the elevators
        elevators.forEach(function(elevator) {
            elevator.on("idle", function() {
                // When the elevator is idle, move it to the floor with the most passengers waiting
                var mostPopulatedFloor = getMostPopulatedFloor(floors);
                elevator.goToFloor(mostPopulatedFloor);
            });
        });
    },
    update: function(dt, elevators, floors) {
        // Check for passengers waiting on floors and direct elevators to those floors
        floors.forEach(function(floor) {
            if (floor.hasWaitingPassengers()) {
                var nearestElevator = getNearestElevator(elevators, floor);
                nearestElevator.goToFloor(floor.floorNum());
            }
        });
    }
}
```

This is just one possible way to solve the challenge, and you may need to adjust your approach depending on the specific requirements of the challenge.

# Can you implement the getMostPopulatedFloor function?

Yes, here is an example implementation of the getMostPopulatedFloor function, which takes in an array of floor objects and returns the floor with the most passengers waiting:

```
function getMostPopulatedFloor(floors) {
    var mostPopulatedFloor = null;
    var maxWaitingPassengers = 0;
    floors.forEach(function(floor) {
        var numWaitingPassengers = floor.numWaitingPassengers();
        if (numWaitingPassengers > maxWaitingPassengers) {
            maxWaitingPassengers = numWaitingPassengers;
            mostPopulatedFloor = floor;
        }
    });
    return mostPopulatedFloor;
}
```

This function iterates over all of the floors and keeps track of the floor with the most passengers waiting. It does this by calling the numWaitingPassengers function for each floor, which returns the number of passengers waiting on that floor. At the end, it returns the floor with the most passengers.

# Can you implement the getNearestElevator function?

Yes, here is an example implementation of the getNearestElevator function, which takes in an array of elevator objects and a floor object, and returns the elevator that is nearest to the specified floor:

```
function getNearestElevator(elevators, floor) {
    var nearestElevator = null;
    var minDistance = Infinity;
    elevators.forEach(function(elevator) {
        var distance = Math.abs(elevator.currentFloor() - floor.floorNum());
        if (distance < minDistance) {
            minDistance = distance;
            nearestElevator = elevator;
        }
    });
    return nearestElevator;
}
```

This function iterates over all of the elevators and keeps track of the one that is closest to the specified floor. It does this by calling the currentFloor function for each elevator to get the floor that the elevator is currently on, and then calculates the distance between that floor and the specified floor. At the end, it returns the elevator with the smallest distance to the specified floor.

# Can you implement the hasWaitingPassengers method?

Yes, here is an example implementation of the hasWaitingPassengers method, which can be called on a floor object to check if there are any passengers waiting on that floor:

```
function hasWaitingPassengers() {
    return this.numWaitingPassengers() > 0;
}
```

This method simply calls the numWaitingPassengers function for the floor and checks if the returned value is greater than 0. If it is, then there are passengers waiting on the floor and the method returns true, otherwise it returns false. Note that this method should be added as a prototype method of the floor object, so that it can be called on individual floor objects, like this:

```
Floor.prototype.hasWaitingPassengers = function() {
    return this.numWaitingPassengers() > 0;
}
```

Once this method is added to the prototype, you can call it on any floor object like this:

```
var floor = floors[0]; // Get the first floor object from the array
if (floor.hasWaitingPassengers()) {
    // There are passengers waiting on this floor
}
```

# Can we get a better answer by providing the full elevator saga API via typescript?

Let's say these are the APIs for Elevator and Floor:

```
interface Elevator {
    goToFloor: (floor: number, directly?: boolean) => void;
    stop: () => void;
    currentFloor: () => number;
    goingUpIndicator: (set?: boolean) => void;
    goingDownIndicator: (set?: boolean) => void;
    maxPassengerCount: () => number;
    loadFactor: () => number;
    destinationDirection: () => string;
    destinationQueue: number[];
    checkDestinationQueue: () => void;
    getPressedFloors: () => number[];
    on: (events: string, callback: Function) => void;
    one: (events: string, callback: Function) => void;
}

interface Floor {
    floorNum: () => number;
    on: (events: string, Function) => void;
    buttonStates: { down: string, up: string };
}
```

Annotating the skeleton code from the docs:

```
{
    init: function(elevators: Elevator[], floors: Floor[]) {
        // Do stuff with the elevators and floors, which are both arrays of objects
    },
    update: function(dt, elevators: Elevator[], floors: Floor[]) {
        // Do more stuff with the elevators and floors
        // dt is the number of game seconds that passed since the last time update was called
    }
}
```

This is the game's dummy implementation, it doesn't work very well:

```
{
    init: function(elevators: Elevator[], floors: Floor[]) {
        var elevator = elevators[0]; // Let's use the first elevator

        // Whenever the elevator is idle (has no more queued destinations) ...
        elevator.on("idle", function() {
            // let's go to all the floors (or did we forget one?)
            elevator.goToFloor(0);
            elevator.goToFloor(1);
        });
    },
    update: function(dt, elevators: Elevator[], floors: Floor[]) {
        // We normally don't need to do anything here
    }
}
```

Can you write an optimized implementation using the attributes available on the Elevator and Floor objects?


# Response

Here is an example implementation that optimizes the movement of the elevators to transport people in an efficient manner:

```
{
    init: function(elevators: Elevator[], floors: Floor[]) {
        // Set up event listeners for the elevators
        elevators.forEach(function(elevator) {
            // When the elevator is idle, move it to the floor with the most passengers waiting
            elevator.on("idle", function() {
                var mostPopulatedFloor = getMostPopulatedFloor(floors);
                elevator.goToFloor(mostPopulatedFloor);
            });

            // When a floor button is pressed inside the elevator, add that floor to the destination queue
            elevator.on("floor_button_pressed", function(floorNum) {
                elevator.goToFloor(floorNum, true);
            });
        });

        // Set up event listeners for the floors
        floors.forEach(function(floor) {
            // When the up or down button is pressed on a floor, find the nearest idle elevator and go to that floor
            floor.on("up_button_pressed", function() {
                var nearestIdleElevator = getNearestIdleElevator(elevators);
                if (nearestIdleElevator) {
                    nearestIdleElevator.goToFloor(floor.floorNum(), true);
                }
            });
            floor.on("down_button_pressed", function() {
                var nearestIdleElevator = getNearestIdleElevator(elevators);
                if (nearestIdleElevator) {
                    nearestIdleElevator.goToFloor(floor.floorNum(), true);
                }
            });
        });
    },
    update: function(dt, elevators: Elevator[], floors: Floor[]) {
        // Check for passengers waiting on floors and direct elevators to those floors
        floors.forEach(function(floor) {
            if (floor.hasWaitingPassengers()) {
                var nearestElevator = getNearestElevator(elevators, floor);
                nearestElevator.goToFloor(floor.floorNum(), true);
            }
        });
    }
}
```

This implementation sets up event listeners for the elevators and floors, so that the elevators can respond to different events in an appropriate manner. For example, when an elevator is idle, it moves to the floor with the most passengers waiting, and when a floor button is pressed inside an elevator, that floor is added to the destination queue for that elevator.

The update function is used to check for passengers waiting on floors and direct elevators to those floors if necessary. This is done by iterating over all of the floors and calling the hasWaitingPassengers method on each floor to check if there are any passengers waiting. If there are, the nearest elevator is found and directed to that floor.

This is just one possible way to implement an optimized solution for this challenge, and you may need to adjust your approach depending on the specific requirements of the challenge.

# Can you implement the getNearestIdleElevator function?

Yes, here is an example implementation of the getNearestIdleElevator function, which takes in an array of elevator objects and returns the nearest idle elevator:

```
function getNearestIdleElevator(elevators) {
    var nearestIdleElevator = null;
    var minDistance = Infinity;
    elevators.forEach(function(elevator) {
        if (elevator.destinationQueue.length === 0) {
            // This elevator is idle, check its distance to the current floor
            var distance = Math.abs(elevator.currentFloor() - currentFloor);
            if (distance < minDistance) {
                minDistance = distance;
                nearestIdleElevator = elevator;
            }
        }
    });
    return nearestIdleElevator;
}
```

This function iterates over all of the elevators and checks if each one is idle (i.e. has an empty destination queue). If an elevator is idle, the distance between that elevator and the current floor is calculated and compared to the minimum distance found so far. If the distance is smaller, the elevator is set as the nearest idle elevator and the minimum distance is updated. At the end, the nearest idle elevator is returned.

Note that this function assumes that there is a global variable called currentFloor that specifies the floor that the elevator is currently on. This variable should be updated whenever the current floor changes, so that the function can accurately calculate the distances between the elevators and the current floor. You may need to adjust this implementation depending on how you are tracking the current floor in your code.
