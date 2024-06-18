
### Leo Finalize Update

The finalize programming model for on-chain code has been replaced with a new async/await model.

The key difference between the two models is that instead of a finalize block, on-chain code is defined in an async function. Note that if a program does not declare on-chain code (there are not finalize blocks) then it will work without modification under the async/await model.

### Previous Testnet3 Leo version Finalize model
program test1.aleo {

    //sample mapping data
    mapping data: u32 => u32; 

    //transition
    transition main(public x: u32) -> (u32) {
        //finalize `main` called in the finalize state below, must match with transition name
        return (x) then finalize(x);
    }

    finalize main (x: u32){
    //add up x value to x_1
    let x_1: u32 = x + 2u32;
    //add up x value to x_2
    let x_2: u32 = x + 5u32;
    //conditional statement check if x_1 is greater than x_2
    //if true, output x_1 otherwise x_2 to mapping (data)
    x = (x_1 > x_2) ? x_1 : x_2;
    Mapping::set(data, 0u32, x);
    } }
    
### Testnet Beta Leo version Finalize model
program test1.aleo {

    //sample mapping data
    mapping data: u32 => u32; 

    //async transition
    async transition main(public x: u32) -> (u32, Future) {
        //function `finalize_main` called in the async state below
        return (x, finalize_main(x));
    }

    async function finalize_main (x: u32){
    //add up x value to x_1
    let x_1: u32 = x + 2u32;
    //add up x value to x_2
    let x_2: u32 = x + 5u32;
    //conditional statement check if x_1 is greater than x_2
    //if true, output x_1 otherwise x_2 to mapping (data)
    x = (x_1 > x_2) ? x_1 : x_2;
    Mapping::set(data, 0u32, x);
    } }


### Key Concepts
1. Async Functions and Transitions:

- Async functions can be defined to run asynchronously. These functions return a Future, which represents code that will be executed later.
- Async transitions are special kinds of async functions that can be called to perform state changes on the blockchain.

2. Futures:

- A Future is an object representing a value that will be available at some point in the future. In the async/await model, functions do not return values directly; instead, they return Futures.

### Rules of Async/Await Model
1. Calling Async Functions:

- Async functions can only be called from within an async transition.
- Async functions cannot return values directly; they return a Future.

2. Async Transitions:

- Async transitions cannot be called inside a conditional block.
- An async transition call returns an optional value and must return a single Future.
- The Future produced by an async transition call cannot be returned.

3. Handling Futures:

- A Future can be passed into an async function call and must be awaited.
- All Futures must either be consumed by an async function call or returned as an output.
