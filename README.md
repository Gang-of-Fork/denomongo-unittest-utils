# denomongo-unittest-utils
Simple [Deno](https://deno.land) Module to provide Spy Mocks for creating Unittests with [mongo](https://deno.land/x/mongo)

## Purpose
The purpose of this module is to help with creating Unit-Tests for applications that use the [mongo driver for deno](https://deno.land/x/mongo). The module provides Spy Mocks for Colletions, meaning that their behaviour can be adjusted and function calls can be asserted with the [mock](https://deno.land/x/mock) package.

## Prerequisites
To use this module efficiently, you should outsource each mongo collection into a seperate file and export the collection as default. By doing this, you can easily redirect the imports of this collection easily via an [import map](https://deno.land/manual/linking_to_external_code/import_maps) as shown in the usage example below.

## Installation
This is a [Deno](https://deno.land) Module available at the [deno registry](https://deno.land/x/denomongo-unittest-utils). 
Before looking at the usage example it, [download and install Deno](https://deno.land/#installation).

## Usage Example
The following usage example can be viewed in /examples/simple_example and refer to the README for instructions to run the code yourself.
### exampletest.ts
```ts
//prodCollection will be replaced by the mockCollection via import map
import prodCollection from "./prodCollection.ts";
import { MockCollection } from "../../mod.ts"
import { Filter, FindOptions } from "https://deno.land/x/mongo@v0.29.2/mod.ts"
import {assertSpyCallAsync, Spy} from "https://deno.land/x/mock@0.13.0/mod.ts"

//example function to be tested
function exampleDatabaseCall() {
    prodCollection.findOne({ id: "example" }, {limit: 1})
}


Deno.test("simple example", () => {
    //define the MockCollections behaviour when calling the findOne function
    MockCollection.initMock({
        findOne: (_filter?: Filter<unknown> | undefined, _options?: FindOptions | undefined): Promise<unknown> => {
            return new Promise((resolve, _reject) => {
                resolve({ id:"example"})
            })
        }
    })

    //execute the function
    exampleDatabaseCall()

    //check whether the findOne Method was called correctly and returned the correct values
    assertSpyCallAsync(MockCollection.getInstance().findOne as Spy<any>, 0, {args: [{id: "example"}, {limit: 1}], returned: {id: "example"}})

})
```
### import_map.json

### prodCollection.ts

