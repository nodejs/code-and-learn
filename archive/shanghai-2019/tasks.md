# Code and learn planning

https://github.com/nodejs/code-and-learn/issues/97

Process:

- Before the session, we give an introduction about the code base, including
  - How to build and run tests
  - Directory structure: lib, src, test, benchmark
  - How to submit a pull request
  - What is necessary for a PR to land (code reviews and CI)
- After settling down on the tasks, we assign codes for each one of them.
- Participants are expected to
  - Pick a task based on the prerequisites
  - Find a file (and possibly related files) to change according to the clues given
  - Report the code along with the file that they are going to change in a place that's visible to other participants to avoid stepping on each other's toes.
  - **Focus on one file at a time, instead of changing all the files with the provided patterns in bulk** - changing multiple files in bulk results in unnecessary churn for backporters and difficulties in code reviews.
- If both `lib/a.js` and `lib/b.js` contain the pattern provided in the same task, it's recommended to only change `lib/a.js` in one commit and submit a PR for that, and move on to work on another task, leaving `lib/b.js` to other participants. Working on different tasks instead of repeating just one is much more helpful in improving understanding of the code base.
- Participants are encouraged to work on tasks with prerequisites that they don't have. They can ask the mentors on-site for help and learn more about the code base.

Please report: `task-code + filename.js` in the issue https://github.com/nodejs/code-and-learn/issues/97 before you start working on a file. For example if you plan to change the `Object.assign` pattern in `test/parallel/test-something.js`, reply in the issue:

```
I am going to work on object-assign in test/parallel/test-something.js
```

The task codes can be found below.

## JavaScript tasks

Prerequisites:

- Basic knowledge about ES6 and later

### Replace `Object.assign()` with object spread

Task code: `object-assign`

Look for patterns like this:

```js
Object.assign({ key: value }, obj)
```

And replace it with

```
{ key: value , ...obj }
```

### Replace `var` with `const`/`let`

Task code: `var`

Find `var` declarations under `lib/` or `test/` (excluding `test/fixtures/`) and replace them with `const` or `let` where it makes sense.

### Destruct primordials

Task code: `primordials`

See issue: https://github.com/nodejs/node/issues/29766
Example: https://github.com/nodejs/node/pull/29633

Look for patterns like this under `lib/`:

```js
const { Object } = primordials;
...
Object.keys(...);
```

And destruct them further like this:

```js
const { Object: { keys: ObjectKeys } } = primordials;
ObjectKeys(...);
```

## C++ tasks

Prerequisites:

- Basic Knowledge about C++
- Basic understanding of the `node::Environment` class

### Persist strings that are used multiple times in the `Environment`

Task code: `env-string`

Look for patterns like this under `src/`:

```cpp
FIXED_ONE_BYTE_STRING(isolate, "something");
```

If there is a `something_string` in the `PER_ISOLATE_STRING_PROPERTIES` list in `src/env.h`, replace it with `env->something_string()` (when `env` is available somehow at the call site).

If this can be invoked multiple times at runtime (e.g. because it's in a C++ binding that can be called multiple times from the JavaScript land), add `something_string` to the `PER_ISOLATE_STRING_PROPERTIES` list and replace the `FIXED_ONE_BYTE_STRING` call with `env->something_string()` because it's more efficient (the former results in copies of the string).

### Remove unnecessary `Environment::GetCurrent`

Task code: `get-current`

Look for patterns like this under `src/`:

```cpp
void SomeCallback(const FunctionCallbackInfo<Value>& args) {
  Environment* env = Environment::GetCurrent(args);
  ...
}
```

Where `env` is only used to retrieve `env->isolate()` and/or `env->context()`. These may be replaced by `args.GetIsolate()` or `args.GetIsolate()->GetCurrentContext()` because they involve fewer memory reads. It is usually fine, however, to keep the `Environment::GetCurrent` call if `env` is used for other purposes in the binding. If you are not sure whether the other uses of `env` can be done in a more efficient way without using `env`, ask the mentors!

### Adding error codes to JavaScript errors thrown in C++

Task code: `cpp-error`

See issue: 

Look for patterns like this under `src/`:

```cpp
env->ThrowError("some message");
```

Look at the error codes defined in the  `ERRORS_WITH_CODE` list in `src/node_errors.h`, and see if there is already a sensible code for this error. If not, create one like this (the second argument can be other error types that make sense):


```cpp
  V(ERR_SOME_CODE, Error)  \
```

If it's a new error, add an error code associated with that message to the `PREDEFINED_ERROR_MESSAGES` in `src/node_errors.h` like this:

```cpp
  V(ERR_SOME_CODE, "some message") \
```

Otherwise see if the `PREDEFINED_ERROR_MESSAGES` for this code make sense to replace `"some message"` and just use it if it does. If you need to make the message more flexible, ask for help from the mentors or try using your C++ skills to figure it out!

Then replace the call of `env->ThrowError(...)` with

```cpp
THROW_ERR_SOME_CODE(env);
```

Documentation and test updates are required for this task. Find existing tests under `test/` that trigger this error, and make sure `error.code` matches e.g. `ERR_SOME_CODE`. Add tests if they don't already exist. If you add a new `ERR_SOME_CODE` instead of using an existing one, update the `Node.js Error Codes` session in `doc/api/errors.md`.

### Updating memory tracker info for subclasses of `MemoryTracker`

Task code: `memory-tracker`

Look for patterns like this under `src/`

```cpp
SET_MEMORY_INFO_NAME(ClassName)
```

read the source of that class and find all the members. If there are any non-trivial types that are supported by the `MemoryTracker::TrackField` methods defined in `src/memory_tracker.h`, make sure they are all tracked in the `MemoryInfo` virtual method (override it if there is only a `SET_NO_MEMORY_INFO()`).

This adds more details to the instances of these wrappers displayed in the v8 heap snapshots.

## Open C++ tasks

Prerequisites:

- Basic Knowledge about C++
- Knowledge about the Node.js subsystem that you are touching (i.e. what the code actually does, whether it's performance-cirtical, etc.)

### Add Debug calls in C++

Task code: `cpp-debug`

Find `ASSIGN_OR_RETURN_UNWRAP(wrap, ..)` or `SomeType* wrap = Unwrap<SomeType>(...)` calls in `src/`, and add `Debug(wrap, "...")` messages where it makes sense. This results in logs being printed when the environment variable `NODE_DEBUG_NATIVE` is set to a related debug category.

This is not necessary if the JavaScript method calling the C++ binding already uses something like `debug()` to log the debug information (which is printed when `NODE_DEBUG` is set to a related debug category).

### Mark the side effect of the C++ bindings

Task code: `side-effect`

Look for `env->SetMethod(...)` under `src/`, and replace it with `env->SetMethodNoSideEffect(...)` if the method does not result in side effects. Similarly, `env->SetProtoMethod(..)` could be replaced with `env->SetProtoMethodNoSideEffect(...)` where it makes sense.

A good rule of thumb: if it makes sense to eager-evaluate the return result in REPL multiple times and the method always returns the same thing when the arguments are the same, it usually makes sense to mark it as a method without side effects. Also, a method with a name starting with "get" is more likely to be side-effect-free (but not always).
