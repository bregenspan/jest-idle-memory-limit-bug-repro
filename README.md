# jest-idle-memory-limit-bug-repro

This reproduces a bug (or at least an undocumented caveat) that occurs in Jest 29.7.0
when using the `--workerIdleMemoryLimit` option combined with `--runInBand`.

`--runInBand` is often used for debugging purposes to force Jest to run tests within
its parent process, and documents like [Troubleshooting](https://jestjs.io/docs/troubleshooting#tests-are-failing-and-you-dont-know-why)
rely on this behavior. But recent versions of Jest 29.x fork a worker when `--runInBand`
is used combined with `--workerIdleMemoryLimit`.

### Expected Behavior

To see expected behavior, run:

* `npm install`
* `node --inspect-brk ./node_modules/.bin/jest --runInBand`

Follow [Troubleshooting](https://jestjs.io/docs/troubleshooting#tests-are-failing-and-you-dont-know-why) instructions:

* Open Chrome DevTools
* Click through to DevTools for Node.js
* Continue past the first breakpoint
* Observe that the debugger is paused on the `debugger` line in the included example test.

### Actual Behavior

This time, run with the `workerIdleMemoryLimit` option set:

* `node --inspect-brk ./node_modules/.bin/jest --runInBand --workerIdleMemoryLimit="1024MB"`

* Open Chrome DevTools
* Click through to DevTools for Node.js
* Continue past the first breakpoint
* Observe that the debugger does not pause on the `debugger` line in the example test. That's because the test is
  being run in a worker, not "in-band".

