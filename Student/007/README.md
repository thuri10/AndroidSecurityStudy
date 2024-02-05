
<!— @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} —>

<!— code_chunk_output —>

- [Preface] (#Preface)
- [1.ios hooking search Source Resolution] (#1ios-hooking-search-Source Resolution)
- [2.ApiResolver scraps all symbols in memory] (#2apiresolver - scraps all symbols in memory)
- [3. Enumeration Search All Classes/All Methods/All Overloads] (#3 Enumeration Search All Classes All Methods All Overloads)
- [4.hook all classes/all methods/all overloads] (#4hook all classes all methods all overloads)
- [5. Output (modify) parse parameters/call stack/return value] (#5 Output modify parse parameters call stack return value)
- [(1) modify return value implementation purpose] (#1 modify return value implementation purpose)
- [(2) Modify Parameter Implementation Purpose] (#2 Modify Parameter Implementation Purpose)
- [(3) View Call Stack] (#3 View Call Stack)
- [(4) Replacement Function] (#4 Replacement Function)
- [6. Enumerate all modules/symbols/addresses in memory] (#6 Enumerate all module symbol addresses in memory)
- [7. Brainless Automation hook All Functions Under App Package] (#7 Brainless Automation hook - All Functions Under App Package)
- [8.Objection Memory Roaming Scratches All Objects] (#8objection- Memory Roaming Scratches All Objects)
- [9.ObjC.choose enumerates all class output properties] (#9objcchoose enumerates all class output properties)
- [10. Actively Invoking Object Methods to Obtain Algorithm Execution Results] (#10 Actively Invoking Object Methods to Obtain Algorithm Execution Results)
- [11 Configure RPC Black Box Calling Algorithm Remote Bulk Offline] (#11 Configure RPC Black Box Calling Algorithm Remote Bulk Offline)

<!— /code_chunk_output —>

# Preface

Finally we learned the part of Frida, in this paper we will learn to use the ApiResolver scraper built in Frida to enumerate all symbols in memory, including all classes/all methods/all overloads, from the source code of objection automatic hook framework. Then we will output the return value of the parameter call stack after hook, and modify the parameters and return value in order to achieve the purpose of modifying the function logic.

One-time hook function is not the final purpose, batch automation Hundreds of one-time hook function is really the bully, in this paper we also introduce a method to scrape all the classes of App and all the hook, and use the traversal memory property of Frida to scrape the ObjC object, directly call the method to get the algorithm's execution results, and finally configure RPC to make remote batch offline calls to achieve the purpose that team members can still get the algorithm's execution results even if they don't know the details of the algorithm.

Accessories APP, code, etc. are in my project, you can take it from:

[https://github.com/r0ysue/AndroidSecurityStudy](https://github.com/r0ysue/AndroidSecurityStudy)

# 1.ios hooking search Source Parsing

Here we go straight to github[download](https://github.com/sensepost/objection)objection source code to see how its hook for ios is written

ios-related hook code in objection is in the agent—>src—>ios—>hooking.ts file, here we mainly look at how its search and watch implementation.

```swift
const objcEnumerate =(pattern: string): ApiResolverMatch[] => {
    return new ApiResolver('objc').enumerateMatches(pattern);
};

export const search =(patternOrClass: string): ApiResolverMatch[] => {

    // if we didnt get a pattern, make one assuming its meant to be a class
    if(!patternOrClass.includes('['))
        patternOrClass = `*[*${patternOrClass}* *]`;

        return objcEnumerate(patternOrClass);
};
```

Here, we first look at the search source code, we can see that the parameters we passed in as the patternOrclass parameters, first check whether it is the corresponding format, if not the corresponding format, then complete it, and then use ApiResolverMatch to search the symbols in memory to find the address of the hook method, the hook of ios and the hook of so layer in android are all directly to hook the address, and then look at the watch method

```swift
export const watch =(patternOrClass: string, dargs: boolean = false, dbt: boolean = false,
dret: boolean = false, watchParents: boolean = false): void => {

    // Add the job
    // We init a new job here as the child watch* calls will be grouped in a single job.
    // mostly commandline fluff
    const job: IJob = {
        identifier: jobs.identifier(),
        invocations: [],
        type: `ios-watch for: ${patternOrClass}`,
    };
    jobs.add(job);

    const isPattern = patternOrClass.includes('[');

    // if we have a patterm we'll loop the methods, hook and push a listener to the job
    if(isPattern === true){
        const matches = objcEnumerate(patternOrClass);
        matches.forEach((match: ApiResolverMatch)=> {
        watchMethod(match.name, job, dargs, dbt, dret);
        });

    return;
}

watchClass(patternOrClass, job, dargs, dbt, dret, watchParents);
};
```

When the watch parameter is used, it is added to the jobs list and the watchClass method is called

```ObjectiveC
const watchClass =(clazz: string, job: IJob, dargs: boolean = false, dbt: boolean = false,
dret: boolean = false, parents: boolean = false): void => {

const target = ObjC.classes[clazz];

if(!target){
send(`${c.red(`Error!`)} Unable to find class ${c.redBright(clazz)}!`);
return;
}

// with parents as true, include methods from a parent class,
// otherwise simply hook the target class' own methods
(parents ? target.$methods : target.$ownMethods).forEach((method)=> {
// filter and make sure we have a type and name. Looks like some methods can
// have "as name... am expecting something like "- isJailBroken"
const fullMethodName = `${method[0]}[${clazz} ${method.substring(2)}]`;
watchMethod(fullMethodName, job, dargs, dbt, dret);
});

};
```

You can see that the watchClass method first gets the class object by passing the parameter clazz as a parameter using api ObjC.classes[clazz], and then traverses the class method to call the watchMethod method to the method hook.

```ObjectiveC
    const watchMethod =(selector: string, job: IJob, dargs: boolean, dbt: boolean,
    dret: boolean): void => {

    const resolver = new ApiResolver("objc");
    let matchedMethod = {
        address: undefined,
        name: undefined,
    };

    // handle the resolvers error it may throw if the selector format is off.
    try {
        // select the first match
        const resolved = resolver.enumerateMatches(selector);
        if(resolved.length <= 0){
        send(`${c.red(`Error:`)} No matches for selector ${c.redBright(`${selector}`)}. ` +
        `Double check the name, or try "ios hooking list class_methods" first.`);
        return;
    }

    // not sure if this will ever be the case... but lets log it
    // anyways
    if(resolved.length > 1){
        send(`${c.yellow(`Warning:`)} More than one result for selector ${c.redBright(`${selector}`)}!`);
    }

    matchedMethod = resolved[0];
    } catch(error){
    send(
        `${c.red(`Error:`)} Unable to find address for selector ${c.redBright(`${selector}`)}! ' +
        `The error was:\n` + c.red((error as Error).message),
        );
    return;
}
```

// Attach to the discovered match

// TODO: loop correctly when globbing
send