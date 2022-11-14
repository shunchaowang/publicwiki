# Class, Interface, Type

# `unknown` vs `any`
- You can assign anything to `unknown` type but you have to do a type check or type assertion to operate on `unknown`
- You can assign anything to `any` type and you can perform any operation on `any`

Examples
1. The case of `unknown`, a type check is required
```
function func(callback: unknown) {
    if (typeof callback === 'function') 
        callback()
}

func(1) // this will throw type error Object is of type 'unknown' on compiling
```

2. The case of `any`, any operation is allowed
```
function func(callback: any) {
        callback()
}

func(1) // this will throw error during runtime
```

# `declare module`

