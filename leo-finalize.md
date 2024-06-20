
# Leo Finalize Update

The finalize programming model for on-chain code has been replaced with a new async/await syntax.

The key difference between the two syntax is that instead of a finalize block, on-chain code is defined in an async function. Note that if a program does not declare on-chain code (there are not finalize blocks) then it will work without modification under the async/await syntax.

## Previous Testnet3 Leo version Finalize syntax
program test1.aleo {

    // Sample mapping data to store global state
    mapping data: u32 => u32; 

    // Async transition to perform on-chain logic
    transition main(public x: u32) -> u32 {
    
        // Off-chain logic: Compute a value based on input x
        let off_chain_result: u32 = compute_off_chain(x);

        // On-chain logic: Use the off-chain result to update the global state
        return off_chain_result then finalize (off_chain_result));
        
    }

    // Async function to finalize the update of the global state on-chain
    
    finalize main (off_chain_result: u32) {
    
        // Add up off_chain_result value to x_1
        let x_1: u32 = off_chain_result + 2u32;

        // Add up off_chain_result value to x_2
        let x_2: u32 = off_chain_result + 5u32;

        // Conditional statement: check if x_1 is greater than x_2
        // If true, output x_1, otherwise output x_2 to mapping (data)
        let final_value: u32 = (x_1 > x_2) ? x_1 : x_2;

        // Set the value in the mapping
        Mapping::set(data, 0u32, final_value);
    }

    // Off-chain function to compute a value based on input x
    
    function compute_off_chain(x: u32) -> u32 {
    
        // Simple computation: Double the input value
        return x * 2u32;
        
    }
}

    
## Testnet Beta Leo version Finalize syntax
program test1.aleo {

    // Sample mapping data to store global state
    mapping data: u32 => u32; 

    // Async transition to perform on-chain logic
    
    async transition main(public x: u32) -> (u32, Future) {
    
        // Off-chain logic: Compute a value based on input x
        let off_chain_result: u32 = compute_off_chain(x);

        // On-chain logic: Use the off-chain result to update the global state
        return (off_chain_result, finalize_update(off_chain_result));
        
    }

    // Async function to finalize the update of the global state on-chain
    
    async function finalize_update(off_chain_result: u32) {
    
        // Add up off_chain_result value to x_1
        let x_1: u32 = off_chain_result + 2u32;

        // Add up x value to x_2
        let x_2: u32 = off_chain_result + 5u32;

        // Conditional statement: check if x_1 is greater than x_2
        // If true, output x_1, otherwise output x_2 to mapping (data)
        let final_value: u32 = (x_1 > x_2) ? x_1 : x_2;

        // Set the value in the mapping
        Mapping::set(data, 0u32, final_value);
        
    }

    // Off-chain function to compute a value based on input x
    
    function compute_off_chain(x: u32) -> u32 {
    
        // Simple computation: Double the input value
        return x * 2u32;
        
    }
}



## Key Concepts
1. Async Functions and Transitions:

- Async functions can be defined to run asynchronously. These functions return a Future, which represents code that will be executed later.
- Async transitions are special kinds of async functions that can be called to perform state changes on the blockchain.

2. Futures:

- A Future is an object representing a value that will be available at some point in the future. In the async/await model, functions do not return values directly; instead, they return Futures.

### Rules of Async/Await Syntax
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
